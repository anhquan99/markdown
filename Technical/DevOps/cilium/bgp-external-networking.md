# BGP - External Network
## Egress gateway
- When a pod sends traffic to an external IP, the node forwards the packet and performs source NAT - wrap its IP into that packet. The external service will see the node's IP as source not the pod IP. The node tracks the connection so it can reverse NAT (DNAT) responses back to the original pod.
![](/image/Pasted%20image%2020260611134342.png)
- Egress gateway changes this default by routing selected pod traffic through a designated egress node(s).
- Matching traffic is routed—typically encapsulated over the cluster overlay (VXLAN, Geneve, etc.)—to the chosen egress node. That node then SNATs to a configured egress IP and sends the traffic to the external destination. Responses return to the egress node and are reverse-translated and forwarded back to the source pod.
### Use cases
- Enforce outbound traffic routing through a fixed public IP for compliance or firewall whitelisting.
- Centralize audit or logging for outbound connections.
- Apply consistent network policy or inspection at a single egress point.
### Configuration
- Config:
```yaml
bpf:
  masquerade: true

kubeProxyReplacement: true

egressGateway:
  # Enables egress gateway to redirect and SNAT traffic that leaves the cluster.
  enabled: true
```
- Manifest:
```yaml
apiVersion: cilium.io/v2
kind: CiliumEgressGatewayPolicy
metadata:
  name: egress-sample
spec:
  selectors:
    - podSelector:
        matchLabels:
          app: app1
  destinationCIDRs:
    - "4.0.0.0/8"
  egressGateway:
    # Select the node that will act as the gateway for this policy.
    nodeSelector:
      matchLabels:
        node.kubernetes.io/name: node3
    # IP address used to SNAT matched traffic. This IP must exist on the selected node.
    egressIP: 192.168.1.3
```
- Verify on Cilium pod:
```bash
kubectl -n kube-system exec ds/cilium -- cilium-dbg bpf egress list
```

|Column|Description|
|---|---|
|Source IP|Pod IP that matches an egress policy|
|Destination CIDR|The destination range for which the policy applies|
|Egress IP|IP address used to SNAT traffic when it leaves the cluster|
|Gateway IP|Internal IP of the node acting as the egress gateway|
## LoadBalancer IPAM
- In cloud providers (for example, EKS on AWS) creating a Service of type LoadBalancer usually triggers the cloud provider to provision a load balancer and return an external IP or DNS name (for example, an AWS ELB DNS entry).
- On-premise clusters typically use components like MetalLB to provide external IPs.
- Cilium’s LoadBalancer IPAM eliminates the need for separate tooling by allocating external IPs directly from configured pools and exposing them as the Service EXTERNAL-IP.
- Traffic sent to that EXTERNAL-IP will be routed to the Service and then to the pods backing it, provided the network fabric advertises or forwards that IP to the cluster.
### Configuration
```yaml
apiVersion: cilium.io/v2alpha1
kind: CiliumLoadBalancerIPPool
metadata:
  name: blue-pool
spec:
  blocks:
    - cidr: "10.0.10.0/24"
    - cidr: "2004::0/64"
    - start: "20.0.20.100"
      stop: "20.0.20.200"
    - start: "1.2.3.4"
  serviceSelector:
    matchLabels:
      color: red
```

|Block type|Description|Example|
|---|---|---|
|CIDR|A CIDR range (IPv4 or IPv6) from which Cilium will allocate IPs|`cidr: "10.0.10.0/24"`|
|Start/Stop range|An explicit sequential range between two addresses|`start: "20.0.20.100"`, `stop: "20.0.20.200"`|
|Single IP|A start-only entry to represent a single IP|`start: "1.2.3.4"`|l
### Advertise external IP
- Making the allocated external IPs reachable Allocating an IP on the Service resource is only half the job — you must ensure external routing/forwarding directs traffic for those IPs to the cluster nodes.

| Mechanism                       | When to use                                        | Notes                                                                |
| ------------------------------- | -------------------------------------------------- | -------------------------------------------------------------------- |
| BGP advertisements              | Data center or network fabric supports BGP peering | Use a BGP speaker to advertise pool prefixes to upstream routers     |
| L2 announcements (ARP/NDP)      | Simple L2 networks or when BGP is not available    | Cluster nodes respond to neighbor discovery for allocated IPs        |
| Static routing (network config) | Small deployments or lab environments              | Add static routes pointing to cluster nodes or load balancer devices |
### Verification and troubleshooting
```bash
# Verify the pool CR exists:
kubectl get ciliumloadbalancerippool -A
kubectl describe ciliumloadbalancerippool blue-pool
# Check Service allocation:
kubectl get svc myapp-service -o wide
kubectl describe svc myapp-service
```
- Confirm network reachability:
    - From outside the cluster, ping or curl the EXTERNAL-IP.
    - Use network tools on upstream routers to verify routes or BGP advertisements.
- Logs and Cilium status:
    - Check Cilium agent logs on nodes for IPAM-related messages.
    - Inspect Cilium control plane status (see Cilium documentation for cluster-specific commands).
## BGP (Border Gateway Protocol)
### Network terminology
- **Advertise:** is to announce the availability of a network resource to other devices or networks.
- **Peering:** is a relationship between two separate networks (usually large organizations or Internet Service Providers) that allows them to exchange traffic directly with each other.
- **Loopback:** a **loopback interface** is a virtual, software-only interface that exists inside a networking device (like a router or switch).
- **BGP adjacency:** is the **"handshake" or relationship** established between two routers so they can exchange routing information.
- **AS number:** Autonomous System Number is a unique numerical identifier assigned to a large network (like an ISP, a university, or a massive tech company like Google or Amazon) so that it can be identified by other networks on the internet.
- **ARP Request:** Address Resolution Protocol Request is the "Who is this?" shout that happens inside a local network when one device knows the **IP address** of another device but doesn't know its **MAC address**.
### Routing
![](/image/Pasted%20image%2020260611153242.png)
- BGP lets Cilium with the native routing and advertise the IP to the router.
### Configuration
- Enable BGP:
```yaml
bgpControlPlane:
  # Enables the BGP control plane.
  enabled: true
```
### Resources
| Resource               | Purpose                                                                      | Example                                       |
| ---------------------- | ---------------------------------------------------------------------------- | --------------------------------------------- |
| CiliumBGPClusterConfig | Which nodes run BGP, cluster/local ASN, and peers                            | Select nodes by label and define bgpInstances |
| CiliumBGPPeerConfig    | Peer-specific settings (timers, multihop, families, advertisement selectors) | Referred by peerConfigRef                     |
| CiliumBGPAdvertisement | Which prefixes to advertise (PodCIDR, Service IPs, routes attributes)        | Controls advertisementType and attributes     |
```yaml
apiVersion: cilium.io/v2alpha1
kind: CiliumBGPClusterConfig
metadata:
  name: cilium-bgp
spec:
  nodeSelector:
    matchLabels:
      bgp: "true"
  bgpInstances:
    - name: instance-64000
      localASN: 64000
      peers:
        - name: peer-65000-r1
          peerASN: 65000
          peerAddress: 5.5.5.5
          peerConfigRef:
            name: cilium-peer
---
apiVersion: cilium.io/v2alpha1
kind: CiliumBGPPeerConfig
metadata:
  name: cilium-peer
spec:
  timers:
    keepAliveTimeSeconds: 3
    holdTimeSeconds: 9
  ebgpMultihop: 5
  families:
    - afi: ipv4
      safi: unicast
      advertisements:
        matchLabels:
          advertise: "bgp"
---
apiVersion: cilium.io/v2alpha1
kind: CiliumBGPAdvertisement
metadata:
  name: bgp-advertisements
  labels:
    advertise: bgp
spec:
  advertisements:
    - advertisementType: PodCIDR
      attributes:
        communities:
          standard: ["65000:99"]
        localPreference: 99
    - advertisementType: Service
      service:
        addresses:
          - ClusterIP
          - ExternalIP
          - LoadBalancerIP
      selector:
        matchExpressions:
          - key: somekey
            operator: NotIn
            values: ["never-used-value"]
```
- `timers.keepAliveTimeSeconds` & `holdTimeSeconds`: govern BGP keepalive and hold intervals.
- `ebgpMultihop`: TTL for eBGP multi-hop peerings (required when peer is not directly connected).
- `families`: choose address families (IPv4/IPv6) and safi (e.g., unicast).
- `advertisements.selector`: controls which Advertisement resources a BGPPeer should consult.
### Troubleshooting with Cilium CLI
```bash
# List BGP peers and session state
cilium bgp peers

# Show available/learned routes (defaults to ipv4/unicast)
cilium bgp routes available

# Show routes that Cilium has advertised to a peer
cilium bgp routes advertised
```
- What to check if sessions fail:
	- Verify node selector labels match your CiliumBGPClusterConfig.
	- Check network reachability between peers (TCP/179 and any configured multi-hop TTL).
	- Confirm ASNs and peerAddress values are correct.
	- Inspect Cilium agent/operator logs for BGP negotiation messages.
	- Validate that advertisement selectors (labels) match the CiliumBGPAdvertisement resources.
## L2 Announcement
### How it works?
- When a Service is assigned an IP on the local L2 subnet (for example, 172.16.1.250), hosts on that subnet may ARP for that IP.
- With L2 announce enabled, Cilium elects a node to claim the service IP via an ARP reply using the node’s MAC.
- The external sender directs traffic to the node’s MAC; that node forwards traffic into the cluster to the service endpoints.
- Different services can be announced by different nodes, enabling per‑service placement and failover without running BGP.
![](/image/Pasted%20image%2020260611161012.png)
### When to use
- Use L2 announce when cluster nodes and external clients share the same L2 broadcast domain (same VLAN/subnet).
- Do not use L2 announce to advertise services across routed networks or different subnets — use BGP-based advertisement for routed domains.
### Tradeoffs
- Benefits: Simple to configure for flat networks, no router adjacency or BGP required, per-service assignment and automatic failover via leases.
- Tradeoffs: Works only within the same L2 domain; not suitable for multi‑subnet/routed environments.
### Configuration
- Config:
```yaml
kubeProxyReplacement: "strict"

l2announcements:
  enabled: true
```
- Resource:
```yaml
apiVersion: "cilium.io/v2alpha1"
kind: CiliumL2AnnouncementPolicy
metadata:
  name: l2announcement-policy
spec:
  serviceSelector:
    matchLabels:
      app: myapp
  nodeSelector:
    matchExpressions:
      - key: node-role.kubernetes.io/control-plane
        operator: DoesNotExist
  interfaces:
    - "^eth[0-9]+"
  externalIPs: true
  loadBalancerIPs: true
```

|Field|Purpose|Example|
|---|---|---|
|serviceSelector|Selects Services by label to announce their service IPs|`app: myapp`|
|nodeSelector|Selects the set of nodes allowed to hold leases / respond to ARP|Exclude control-plane nodes via DoesNotExist|
|interfaces|Regex list of network interface names on which to respond to ARP|`^eth[0-9]+` matches eth0, eth1, …|
|externalIPs|When true, advertise Service ExternalIPs|`true`|
|loadBalancerIPs|When true, advertise LoadBalancer service IPs|`true`|
### Verify
```bash
kubectl describe ciliuml2announcementpolicy l2announcement-policy

# Cilium uses Kubernetes Lease objects to assign which node will answer ARP for each service IP. To list leases managed by Cilium
kubectl -n kube-system get lease
# NAME                                      HOLDER                AGE
# cilium-l2announce-default-app1-service   my-cluster-worker2    35m
# cilium-l2announce-default-app2-service   my-cluster-worker     35m
```
- HOLDER shows the node currently answering ARP for the service IP.
- If the holder node fails or loses eligibility, the lease will be acquired by another eligible node, which then begins responding — providing automatic failover.