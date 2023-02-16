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
- K8s helps:
	- Automatic deployment.
	- Scaling and load balancing.
	- Management.
## Concepts and architecture
- Pod:
	- Smallest unit of k8s contain container or multiple containers.
	- Multiple pods can be created and removed to scale your app and distributed across all available worker node.
- Worker node:
	- Architecture:
		- API Server: API for the kubelets to communicate. 
		- Scheduler: Watches for new pods, select worker nodes to run them on.
		- Kube-controller-manager: Watches and controls worker nodes, correct number of pods and more.
		- Cloud-controller-manager: Like kube-controller-manager but for specific cloud provider, knows how to interact with cloud provider resources.
	- Think it as a computer.
	- Run the containers of your application.
	- Nodes are your machines/virtual instances.
	- Multiple worker nodes are controlled by the master node.
	- The master node is a running instance in a different worker node so that if the worker node goes down, the master node will not go down.
- Proxy/config:
	- Connect to outside request and response.
- Kubelet:
	- A software help communicates between master and worker node.
- Kube-proxy:
	- Managed node and pod network communication.
![[Pasted image 20230214120545.png]]
## Terns
- Cluster: a set of node machines which are running the containerized application (worker nodes) or control other nodes (master node).
- Nodes: physical or virtual machine with a certain hardware capacity which hosts one or multiple pods and communicates with the cluster.
	- Master node: Cluster control pane, managing the pods across worker nodes.
	- Worker node: host pods, running app containers (+ resources).
- Pods: holds the actual running app containers + their required resources (e.g. volumes).
- Containers: docker containers.
- Services: a logical (group) of pods with a unique, pod and container independent IP address.
## What you need to do/setup?
- Create the Cluster and the node instances (worker + master nodes)
- Setup API Server, kubelet and other k8s services/ software on nodes.
- Create other (cloud) provider resources that might be needed (e.g. load balancer, file system).
## What k8s will do?
- Create your objects (e.g. pods) and manage them.
- Monitor pods and re-create them, scale pods, ...
- K8s utilizes the provided (cloud) resources to apply your config/goals.
## How to build a kubernetest service:
1. Create image and push it to repository.
2. Start create service by file (Declarative) or by CLI (Imperative)
	- Declarative:
		- Create deployment and service file with extension is .yaml or .yml, here you can have multile file for multiple resources.
		- Use `kubectl apply -f $deployment_file -f $service_file` to apply resource to k8s.
	- Imperative:
3. Use `$service_provider service $service_name` like `minikube service backend` to start start service from service provider.
## [[Object]]