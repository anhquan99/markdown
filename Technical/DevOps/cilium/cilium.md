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
## Installation
### Components
- Agent: DaemonSet - cilium
- Envoy: DaemonSet - cilium-envoy
- Operator: Deployment - cilium-operator
- Configuration: ConfigMap - cilium-config
- Cluster role and binding for:
	- Agent: cilium
	- Operator: cilium-operator
	- Envoy: cilium-envoy
### Methods
- Cilium CLI
```ad-note
There is another Cilium CLI used in agent pod, used for advanced debugging
```
- Helm