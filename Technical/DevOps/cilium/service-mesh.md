# Service Mesh
## Definition
- With the introduction of distributed applications, additional visibility, connectivity, and security requirements have surfaced.
- Application components communicate over untrusted networks across cloud and premises boundaries, load-balancing is required to understand application protocols, resiliency is becoming crucial, and security must evolve to a model where sender and receiver can authenticate each other’s identity.
- In the early days of distributed applications, these requirements were resolved by directly embedding the required logic into the applications.
- A service mesh extracts these features out of the application and offers them as part of the infrastructure for all applications to use and thus no longer requires to change each application.
## Features
- **Resilient Connectivity**: Service to service communication must be possible across boundaries such as clouds, clusters, and premises. Communication must be resilient and fault tolerant.
- **L7 Traffic Management**: Load balancing, rate limiting, and resiliency must be L7-aware (HTTP, REST, gRPC, WebSocket, …).
- **Identity-based Security**: Relying on network identifiers to achieve security is no longer sufficient, both the sending and receiving services must be able to authenticate each other based on identities instead of a network identifier.
- **Observability & Tracing**: Observability in the form of tracing and metrics is critical to understanding, monitoring, and troubleshooting application stability, performance, and availability.
- **Transparency**: The functionality must be available to applications in a transparent manner, i.e. without requiring to change application code.
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