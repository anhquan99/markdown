- Nodes are virtual identities assigned by k8s to the system part of the cluster - whether VM, bare-metal, Containers, ... These identities are unique to each system, and are used by the cluster for resources and monitoring purpose, which helps with workload management.
- Each node hosts a container runtime, and it is managed with the help of 2 k8s node agents:
	- `kubelet`
	- `kube-proxy`
- The container runtime is required to run all containerized workload on the node-control plane and user workload.
# Types
- Control plane: run control plane agents such as API Server, Scheduler, ...
- Worker: run the `kubelet`and `kube-proxy` node agents, container runtime, and add-ons for container networking, monitoring, logging, DNS, ...