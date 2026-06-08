# Cilium
## Definition
- Cilium is open source software for transparently securing the network connectivity between application services deployed using Linux container management platforms like Docker and Kubernetes.
- At the foundation of Cilium is a new Linux kernel technology called eBPF, which enables the dynamic insertion of powerful security visibility and control logic within Linux itself. Because eBPF runs inside the Linux kernel, Cilium security policies can be applied and updated without any changes to the application code or container configuration.
## Architecture
![](/image/Pasted%20image%2020260607192725.png)
### Cilium agent
- Runs on every node (typically as a DaemonSet).
#### Responsibilities
- Watch Kubernetes API events (Pods, Services, Endpoints, CRDs) and translate cluster state into datapath configuration like dynamically update the networking rules based on these events. Examples:
	- New pods or containers start
	- Pods or containers stop
- Compile, load, and attach eBPF programs to the appropriate kernel hooks and network interfaces.
- Enforce network policies and implement service/load-balancing rules using eBPF.
- Route traffic to Envoy when L7 processing is required.
- Host a local Hubble server for per-node flow capture and metrics.
### eBPF
- L3/L4 ingress and egress packet processing (IP/TCP/UDP).
- In-kernel, efficient load balancing for Services.
- Low-overhead enforcement of network and security policies.
### Cilium operator
- The Cilium operator handles cluster-scoped duties that don’t belong to any single node agent. It keeps cluster-wide state synchronized and performs tasks that require global knowledge.
#### Responsibilities
- Synchronize node IPs and lifecycle between Kubernetes and Cilium.
- Coordinate cluster-wide service information so nodes program consistent service maps.
- Manage IP allocation for services when using Cilium’s eBPF-based load balancer.
- Allocate and synchronize security identities (used by network policies).
- Manage CIDRs, CRDs, and IPAM tasks for cluster-scoped resources.
- Assist Cluster Mesh / multi-cluster configuration and CRD lifecycle.
- Aggregate or coordinate Hubble relays and observability across the cluster.
### Envoy proxy
- Can be deployed as sidecar as a part of Cilium agent pod or deployed a standalone pod - 1 proxy per node.
- eBPF can only handle at L3 and L4 level, so the Envoy is used to handle L7 features.
### K8s CRDs
- Cilium installs several Kubernetes CRDs to represent policies, observability settings, and other Cilium-specific configuration. These CRDs provide a Kubernetes-native interface to express Cilium network policies, service mesh configuration, and observability controls across the cluster.
### Hubble server
- Hubble provides high-performance observability built on eBPF flow capture. Each Cilium agent includes a Hubble server for local flow telemetry.
#### Responsibilities
- Per-node Hubble server that streams eBPF-based flow data with low overhead.
- gRPC API and Prometheus metrics endpoints for integration and monitoring.
- Capture of L3/L4 flows and optional L7 telemetry (when Envoy provides L7 context).
- Visibility into packet drops, DNS queries, policy decisions, and more for debugging.
#### Hubble relay server
- Is an optional component.
- Used to aggregates flow data from multiple per node Hubble server to present a centralized cluster-wide view.
- Can view by using GUI or CLI.
### `kube-proxy`
- Can be replaced with Cilium when you want Cilium to handle all the traffic.
- Cilium can perform the `kube-proxy` capabilities.
## Terminology
### Cilium endpoint
- ***Every address is an endpoint***
- An endpoint in Cilium represents a network identity for which Cilium enforces network policy.
- Cilium assigns each endpoint a unique numeric ID that the agent uses internally to manage that endpoint. 
```bash
# inpect enpoints from a Cilium agent
kubectl exec -n kube-system cilium-{agent} -- cilium endpoint list
```
### Cilium identities
- Cilium addresses IP volatility by decoupling policy from IP addresses: it assigns stable numeric identities to sets of endpoints that share the same semantic labels (for example, role=frontend).
- Policies are written against these identities rather than raw IPs, so they remain valid when pods scale or move.
- Benefits:
	- Identities remain stable across Pod lifecycle events.
	- Security policies are decoupled from changing network addresses, simplifying enforcement and scaling.
- All Pods that share the same set of labels are mapped to the same numeric identity.
```bash
# inpect id list
kubectl exec -n kube-system cilium-{agent} -- cilium identity list
```