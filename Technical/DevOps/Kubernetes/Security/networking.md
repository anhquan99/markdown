# Networking
## Terms and expressions
- Ingress filtering: the filtering of data connections, and connection requests not originating from the protected network. It is important to filter all ingress data not specifically required for services provided by the protected network to function.
- Egress filtering: the filtering of packets originating inside the firewall. This is an important part of enforcing acceptable use policies, and reducing the risk that any malware or infected hosts can attack hosts outside the firewall.
- Bridging: the act of aggregating multiple networks to appear as a single network, appearing as a direct connection of 1 network to the other.
- Forwarding: relaying of a packet from 1 node on a network to another. Ports can also be configured to forward on a firewall/router, allowing ingress packets on port A to be forwarded to port B on a host inside the firewall. The decision of what packet is forwarded to which port is part of a routing.
- Network Address Translation (NAT)
## Session state
- Firewalls intercept traffic and use connection tracking to improve performance; this would be impacted if all connections are stateless, but the benefit may be worth the performance difference.
- Many network protocols keep track of the session state:
	- **New** typically refers to the 3-way TCP handshake, and can be the handshake of any similar protocol.
	- The majority of the traffic is likely to be under the **Established** category. Placing a rule that allows established traffic at the beginning of the ruleset can improve performance.
	- **Related** is unique in that not all firewalls are capable of considering a packet as related to an established connection. Netfilter requires additional protocol-specific modules to track related traffic. FTP and VOIP require extra kernel modules to be loaded, whereas replies to DNS queries will automatically be considered as related to a known outbound DNS lookup request.
	- **Invalid** traffic is that which is out of sequence according to the protocol specifications.
## Anatomy of a Filter
- Firewalls are essentially traffic filters. As such, there are three main components of the individual rules used in a firewall to filter traffic.
- Where to apply the filter is generally the first decision made when constructing a filter rule. Ingress traffic is that coming into the system or environment. Egress traffic is that generated inside the environment and leaving the local network.
- Both ingress and egress traffic should be filtered at the network perimeter. Acceptable use policies can be enforced via egress filtering. Egress filtering can also ensure that local systems do not leak sensitive information and cannot attack external systems on the internet, e.g., participate in a botnet DoS, or act as spam mailers.
- Deciding which traffic to filter is done by creating selection criteria and then comparing the traffic attributes to that criteria. The specific action taken is self-explanatory. These concepts apply both to operating system firewalls, as well as Kubernetes `networkpolicies`.
## Certificates
![[image-20.png]]
### `/etc/kubernetes/pki`
- CA (Certificate Authority):
	- `ca.crt`
	- `ca.key`
- API server certificate:
	- `apiserver.crt`
	- `apiserver.key`
- API → `etcd`
	- `apiserver-etcd-client.crt`
	- `apiserver-etcd-client.key`
- API → `kubelet`
	- `apiserver-kubelet-client.crt`
	- `apiserver-kubelet-client.key`
- `etcd` server cert (`/etc/kubernetes/pki/etcd`)
	- `server.crt`
	- `server.key`
- Scheduler → API 
	- `/etc/kubernetes/scheduler.conf`
- Control manager → API
	- `/etc/kubernetes/controller-manager.conf`
- `kubelet` → API
	- `/etc/kubernetes/kubelet.conf` under `client-certificate`
### `/var/lib/kubelet/pki`
- `kubelet` sever certificate