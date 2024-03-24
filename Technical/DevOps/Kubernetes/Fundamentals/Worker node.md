- Provides a running env for client application.
- In K8s the application containers are encapsulated in Pods, controlled by the cluster control plane agents running on the control plane node. Pods are scheduled on worker nodes, where they find required compute, memory and storage resources to run, and networking to talk to each other and the outside world.
  ![[Pasted image 20240324123939.png]]
- A Pod is the smallest scheduling work unit in K8s, isa logical collection of 1 or more containers scheduled together, and the collection can be started, stopped, or rescheduled as a single unit of work.
- In a multi-worker K8s cluster, the network traffic between client users and the containerized applications deployed in Pod is handled directly by the worker nodes, and is not routed through the control plane node.
# Components
- [[Worker node#Container Runtime|Container Runtime]]
- [[Worker node#Node Agent - `kunelet`|Node Agent - kubelet]]
- [[Worker node#Proxy - `kube-proxy`|Proxy - kube-proxy]]
- Add-ons for DNS, Dashboard user interface, cluster-level monitoring and logging
## Container Runtime
- Because K8s has a lack of capability to directly handle and run containers, it requires a container runtime on the node where a Pod and its containers are to be scheduled.
- Runtimes are required on all nodes of a K8s cluster, both control plane and worker.
- Example of container runtime:
	- `CRI-O`
	- `containerd`
	- `Docker Engine`
	- `rkt`
## Node Agent - `kunelet`
- Running on each node, control plane and worker.
- Communicates with the control plane.
- It receives Pod definitions, primarily from the [[Control plane#API Server|API Server]], and interacts with the container runtime on the node to run containers associated with the Pod. It also monitors the health and resources of Pods running containers.
- `kubelet` connects to container runtimes through a plugin based interface - the Container Runtime Interface (CRI). In order to connect to interchangeable container runtimes, `kubelete` uses a **CRI shim**, an application which provides a clear abstraction layer between `kubelet` and the container runtime.
  ![[Pasted image 20240324130822.png]]
## Proxy - `kube-proxy`
- Network agent runs on each node, control plane and workers.
- Responsible:
	- Dynamic updates and maintenance of all networking rules on the node
	- Abstracts the details of Pods networking and forwards connection request (TCP, UDP, and SCTP stream) to the containers in the Pods.
# Notes
- Imagine a pod as a shipping container. It can hold various individual packages (containers) that are all delivered together to a destination (deployment on a server). The packages might be dependent on each other, like parts for a machine.
- In essence, containers are the building blocks of applications, while pods are the units that Kubernetes manages and schedules for deployment.