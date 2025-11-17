# kubernetes
## Definition
- Kubernetes (k8s) is an open-source system for automating deployment, scaling, and management of containerized applications.
- Manual deploy problem:
	- Hard to maintain, error-prone and annoying.
		- Containers might crash/go down and needs to be replaced.
		- We might need more container instances upon traffic spikes.
		- Incoming traffic should be distributed equally.
- Service provider solutions, but because of this, user need to have a knowledge about the service of the provider:
	- Container health checks + automatic re-deployment.
	- Autoscaling.
	- Load balancer (if 2 or more containers running).
### Kubernetes features:
- Automatic bin packing: automatically schedules containers based on resource needs and constraints, to maximize utilization without sacrificing availability.
- Designed for extensibility: cluster can be extended with new custom features without modifying the upstream source code.
- Self-healing.
- Horizontal scaling.
- Service discovery and load balancing.
- Automated rollouts and rollbacks.
- Secret and configuration management.
- Storage orchestration.
- Batch execution.
- IPv4/IPv6 dual-stack.
## Concepts and [[Technical/DevOps/kubernetes/fundamentals/architecture |architecture]]
- Pod:
	- Smallest unit of k8s contain container or multiple containers.
	- Multiple pods can be created and removed to scale your app and distributed across all available worker node.
- Proxy/config:
	- Connect to outside request and response.
![[Pasted image 20240324113009.png]]
## Terms
- **Cluster**: a set of node machines which are running the containerized application (worker nodes) or control other nodes (master node).
- **Nodes**: physical or virtual machine with a certain hardware capacity which hosts one or multiple pods and communicates with the cluster.
	- Master node: Cluster control pane, managing the pods across worker nodes.
	- Worker node: host pods, running app containers (+ resources).
- **Pods**: holds the actual running app containers + their required resources (e.g. volumes).
- **Containers**: containers running application.
- **Services**: a logical (group) of pods with a unique, pod and container independent IP address.
## What you need to do/setup?
- Create the Cluster and the node instances (worker + master nodes)
- Setup API Server, kubelet and other k8s services/ software on nodes.
- Create other (cloud) provider resources that might be needed (e.g. load balancer, file system).
## What k8s will do?
- Create your objects (e.g. pods) and manage them.
- Monitor pods and re-create them, scale pods, ...
- K8s utilizes the provided (cloud) resources to apply your config/goals.
## How to build a Kubernetes service:
1. Create image and push it to repository.
2. Start create service by file (Declarative) or by CLI (Imperative)
	- Declarative:
		- Specify what to do.
		- Create deployment and service file with extension is `.yaml` or `.yml`, here you can have multiple file for multiple resources.
		- Use `kubectl apply -f $deployment_file -f $service_file` to apply resource to k8s.
	- Imperative:
		- Specify how to do.
		- Ex:
			- `kubectl run`
			- `kubectl create`
			- `kubectl expose`
3. Use `$service_provider service $service_name` like `minikube service backend` to start service from service provider.