# Service Mesh
## Definition
- With the introduction of distributed applications, additional visibility, connectivity, and security requirements have surfaced.
- Application components communicate over untrusted networks across cloud and premises boundaries, load-balancing is required to understand application protocols, resiliency is becoming crucial, and security must evolve to a model where sender and receiver can authenticate each other’s identity.
- In the early days of distributed applications, these requirements were resolved by directly embedding the required logic into the applications.
- A service mesh extracts these features out of the application and offers them as part of the infrastructure for all applications to use and thus no longer requires chang each application.
## Features
- **Resilient Connectivity**: Service to service communication must be possible across boundaries such as clouds, clusters, and premises. Communication must be resilient and fault-tolerant.
- **L7 Traffic Management**: Load balancing, rate limiting, and resiliency must be L7-aware (HTTP, REST, gRPC, WebSocket, …).
- **Identity-based Security**: Relying on network identifiers to achieve security is no longer sufficient, both the sending and receiving services must be able to authenticate each other based on identities instead of a network identifier.
- **Observability & Tracing**: Observability in the form of tracing and metrics is critical to understanding, monitoring, and troubleshooting application stability, performance, and availability.
- **Transparency**: The functionality must be available to applications transparently, i.e. without requiring to change application code.
## Benefits
- Traffic management
- Observability
- Security
- Resilience
- Decoupling
- Consistency
- Scalability
- Version control
## Models
### Sidecar
- Deploy as a sidecar container with application container in a pod.
#### Benefits
- No application changes required; language-agnostic.
- Works for immutable third‑party apps.
- Mesh responsibilities are offloaded to the proxy.
#### Drawbacks
- One proxy per pod increases resource usage (CPU/memory).
- Operational complexity for proxy configuration and lifecycle.
	- Manual configuration on every proxy of each application.
- Potential startup and readiness race conditions because proxies must initialize.
- Extra network hop per request increases latency.
### Sidecarless model (Cilium)
- Implement all features into a eBPF program.
- L3, L4 network is handled by the eBPF and the L7 is handled by the Envoy proxy (1 proxy per node).
![](/image/Pasted%20image%2020260607194213.png)
#### Benefits
- Fewer containers per pod and lower aggregate CPU/memory usage.
- Better performance for L3/L4 workloads because eBPF runs in-kernel.
- Reduced complexity from fewer per-pod proxies to manage.
- Compatible with other mesh solutions when needed, enabling hybrid deployments.
#### Drawbacks
- L7 features still require an Envoy proxy component; Cilium optimizes this by using fewer proxies (one per node).
- Some advanced L7 capabilities remain necessarily in user-space proxies.
## Ingress
### Key advantages
- Programs the Linux kernel datapath with eBPF for most routing and load-balancing work, avoiding the need for a separate Ingress-controller pod for L3/L4.
- Uses Envoy only where required: L7 processing, TLS termination (if configured), and policy enforcement.
- Supports both traditional Kubernetes Ingress and the Gateway API for modern traffic routing.
### Configuration
- Config file:
```yaml
nodePort:
  # Enable the Cilium NodePort service implementation.
  enabled: true

ingressController:
  # Enable cilium ingress controller.
  # This will automatically set enable-envoy-config as well.
  enabled: true

  # Set cilium ingress controller to be the default ingress controller.
  # This will let cilium route Ingress entries without an ingressClassName set.
  default: true

  # Default ingress load balancer mode: "dedicated" or "shared"
  # Can also be set via annotation: ingress.cilium.io/loadbalancer-mode: dedicated
  loadbalancerMode: dedicated
```
- Reload:
```bash
helm upgrade cilium cilium/cilium -n kube-system -f values.yaml

# Restart Cilium to pick up config changes
kubectl -n kube-system rollout restart daemonset/cilium
kubectl -n kube-system rollout restart deployment/cilium-operator
```
### Load balancer modes
|Mode|Description|When to use|
|---|---|---|
|dedicated|Each Ingress resource provisions its own external Load Balancer (e.g., AWS ELB/ALB per Ingress).|Use when you need strict isolation per Ingress or separate public endpoints.|
|shared|Multiple Ingress resources share a single external Load Balancer. Cilium programs forwarding rules so a single LB routes traffic to many Ingresses.|Use to minimize cloud LB count and cost; centralize routing and certificates.|
## Gateway API
### Ingress limitation
- Route matching is limited to host and path only — no request-header or query-parameter matching, no method-based routing, and no weighted traffic-splitting.
- Focused primarily on HTTP; lacks standardized first-class support for other protocols such as TCP and gRPC, or richer behaviors (e.g., standardized WebSocket handling).
- Minimal standardized request/response manipulation: no built-in, consistent mechanism for header rewrites, redirects, or rate limiting across implementations.
- Controller-specific configuration is commonly buried in annotations, mixing platform/operator concerns with application routing configuration.
- TLS and routing rules often live in the same Ingress resource, reducing separation of concerns and clear responsibility boundaries.
- Separation-of-concerns is often lost when application teams, platform teams, and operator settings are combined into a single Ingress.
### Separation of concern in gateway
|Resource Type|Purpose|Typical Owner|
|---|---|---|
|GatewayClass|Declares available gateway controllers in the cluster.|Infra / platform teams|
|Gateway|Instantiates and configures a gateway instance (listeners, ports).|Cluster operators|
|Route types (HTTPRoute, TCPRoute, GRPCRoute, etc.)|Define routing policy: hostnames, matches, backends.|Application developers|
![](/image/Pasted%20image%2020260609055442.png)
### Configuration
```yaml
nodePort:
  # Enable the Cilium NodePort service implementation.
  enabled: true

gatewayAPI:
  # Enable support for Gateway API in Cilium.
  # This may automatically enable additional required features (for example_envoy config).
  enabled: true
```
## Encryption
- By default, traffic pod to pod on different nodes inside cluster is not encrypted so the network packets can be captured.
- Cilium provides 2 built-in methods to secure inter-node pod traffic:
	- IPsec
	- WireGuard

| Feature          | IPsec                     | WireGuard                                      |
| ---------------- | ------------------------- | ---------------------------------------------- |
| Performance      | Mature, widely supported  | High-performance, simpler codebase             |
| Key management   | Secret keys in Kubernetes | Keys managed by Cilium (peer-based)            |
| Compatibility    | Works across many kernels | Requires WireGuard support in kernel or module |
| Typical use case | Stable enterprise setups  | High-throughput, low-latency environments      |
### Configuration IPsec
- Manifest:
```yaml
encryption:
  # -- Enable transparent network encryption.
  enabled: true
  # -- Encryption method. Can be either ipsec or wireguard.
  type: ipsec
```
- Reload and verify:
```bash
kubectl -n kube-system rollout restart deployment/cilium-operator
kubectl -n kube-system rollout restart ds/cilium

# verify
kubectl -n kube-system exec -ti ds/cilium -- cilium-dbg encrypt status
```
### Configuration WireGuard
- Manifest:
```yaml
encryption:
  enabled: true
  type: wireguard
```
- Reload and verify:
```bash
kubectl -n kube-system rollout restart deployment/cilium-operator
kubectl -n kube-system rollout restart ds/cilium

# verify
kubectl -n kube-system exec -ti ds/cilium -- cilium-dbg status | grep Encryption
```
### Encryption behavior
- Cilium only encrypts pod-to-pod traffic that traverses the network between different nodes.
- Pods communicating on the same node exchange traffic locally and this traffic is not encrypted by Cilium. This design reduces overhead and is based on the assumption that an attacker
## mTLS
- 2 ways authentications, require both the server and client to authenticate.
- mTLS provides authenticity, integrity, and confidentiality between two communicating endpoints.
- Cilium integrates mTLS using the SPIFFE identity framework.
- SPIRE is the most common open-source SPIFFE implementation and is used by Cilium to issue identities (SVIDs) and certificates that agents use to establish mTLS sessions.
![](/image/Pasted%20image%2020260609064200.png)
### Authentication process
![](/image/Pasted%20image%2020260609064508.png)
1. eBPF looks up the auth table and policy to decide if authentication is required.
2. If authentication is required but not yet established, the initial packet is dropped by eBPF.
3. eBPF notifies the local Cilium agent that authentication is needed.
4. The Cilium agent requests SVIDs from its local Spire agent. The agent may contact the Spire server to obtain certificates.
5. Cilium TLS session managers on the two nodes establish a mutual TLS session using those certificates. (Cilium agents perform the TLS handshake; pods do not.)
6. After successful authentication, the auth table is updated and subsequent packets matching the same policy are allowed and forwarded.

|Component|Role|
|---|---|
|Cilium agent|Manages TLS sessions and enforces policies on behalf of pods.|
|eBPF programs|Enforce network policy and maintain an auth table used to decide forwarding vs. triggering auth.|
|Spire server|Issues SPIFFE Verifiable Identity Documents (SVIDs) to agents.|
|Spire agent|Runs on each node, obtains SVIDs from the Spire server for local workloads.|
|SPIFFE|Identity framework used to represent workload identities (URI-based).|
### Configuration
```yaml
encryption:
  enabled: true
  type: ipsec

authentication:
  enabled: true
  mutual:
    spire:
      # Enable SPIRE integration (beta)
      enabled: true
      install:
        # Enable SPIRE installation (only takes effect if
        # authentication.mutual.spire.enabled is true)
        enabled: true
```
- Enforce mTLS in traffic:
```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: all-server
spec:
  endpointSelector:
    matchLabels:
      app: pod2
  ingress:
  - toPorts:
    - ports:
      - port: "80"
        protocol: TCP
    authentication:
      mode: required
```
### Debug
```bash
# List Cilium agent pods (namespace may vary)
kubectl -n kube-system get pods -l k8s-app=cilium

# Tail logs for a Cilium agent
kubectl -n kube-system logs <cilium-agent-pod> -c cilium --tail=200

# List Cilium agent pods (namespace may vary)
kubectl -n kube-system get pods -l k8s-app=cilium

# Tail logs for a Cilium agent
kubectl -n kube-system logs <cilium-agent-pod> -c cilium --tail=200
```