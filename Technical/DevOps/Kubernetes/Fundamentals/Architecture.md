# High level view
- K8s is a cluster of compute systems categorized by their distinct roles:
- [[Control plane| Control plan nodes]]
- [[Worker node|Worker nodes]] (more then 1 worker nodes is optional but recommended)
![[Pasted image 20240324113009.png]]
# Network challenges
## Container-to-Container communication inside Pods
- When grouping of containers defined by a Pod is started, special infrastructure **Pause container** is initialized by the [[Worker node#Container Runtime|Container Runtime]] for the sole purpose of creating a network namespace for the Pod so that containers can communicate via localhost.
## Pod-to-Pod communication on the same node and across cluster nodes.
 - K8s network model aims to reduce complexity, and it treats Pods as VMs on a network, where each VM is equipped with a network interface - thus each Pod receiving a unique IP address - called **IP-per-Pod** model.
 - Containers are integrated with the overall K8s networking model through the use of the **Container Network Interface** (CNI) supported by CNI plugins.
   ![[Pasted image 20240324132628.png]]
## Service-to-Pod communication within the same namespace and across cluster namespaces.
## External-to-Service communication for client to access applications in a cluster.
- K8s enables external accessibility through Services, complex encapsulations of network routing rule definitions stored in `iptables` on the cluster nodes and implement by `kube-proxy` agent.
- By exposing services to the external world with the aid of `kube-proxy`, applications become accessible from outside the cluster over a virtual IP address and a dedicated port number.
# Cluster configuration
- **All-in-One Single-Node installation**: control plane and worker components are installed and running on a single-node. Useful for learning, but not recommended for production.
- **Single-Control Plane and Multi-Worker installation**: single-control plane node running a stacked `etcd` instance with multiple worker nodes.
- **Single-Control Plane with Single-Node `etcd`, and Multi-Worker installation**: single control plane with external `etcd` and multiple worker nodes.
- **Multi-Control Plane and Multi-Worker installation**: multiple control plane with stacked `etcd` and multiple worker nodes.
- **Multi-Control Plane with Multi-Node `etcd` and Multi-Worker installation**.
# [[Object]]
# [[Autoscaling]]