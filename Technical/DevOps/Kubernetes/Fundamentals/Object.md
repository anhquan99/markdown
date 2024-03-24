# Object model
- Kubernetes became popular due to its advanced application lifecycle management capabilities, implemented through a rich object model, representing different persistent entities in the Kubernetes cluster. Those entities describe:
	- What containerized applications we are running.
	- The nodes where the containerized applications are deployed.
	- Application resource consumption.
	- Policies attached to applications, like restart/upgrade policies, fault tolerance, ingress/egress, access control, etc.
- With each object, we declare our intent, or the desired state of the object, in the **spec** section. The Kubernetes system manages the **status** section for objects, where it records the actual state of the object. At any given point in time, the Kubernetes Control Plane tries to match the object's actual state to the object's desired state. An object definition manifest must include other fields that specify the version of the API we are referencing as the **apiVersion**, the object type as **kind**, and additional data helpful to the cluster or users for accounting purposes - the **metadata**.
- Examples of Kubernetes object types are Nodes, Namespaces, Pods, ReplicaSets, Deployments, DaemonSets, ...
# Pod
- The smallest "unit" k8s interacts with:
	- Contains and runs one or multiple containers.
	- Pods contain shared resources for all Pod containers.
	- Has a cluster-internal IP by default.
- Pods are designed to be ephemerals: k8s will start, stop and replce them as needed.
# Deployment
- Control Pod(s).
	- You set a desired state, k8s then changes the actual state.
	- Deployments can be paused, deleted and rolled back.
	- Deployments can be scaled dynamically and automatically.
- So deployments manage a Pod for you, you can also create multiple Deployments, therefor you don't directly control Pods, instead you use deployments to set up desired end state.
- 2 types of deployments:
	- Imperative
	- Declarative
# Service
- Expose Pods to the Cluster or Externally.
	- Pods have an internal IP by default - it changes when a Pod is replaced.
	- Services group Pods with a shared IP.
	- Services can allow external access to Pods.
- Reaching a Pod from outside the Cluster is not possible at all without Services
# [[Technical/DevOps/Kubernetes/Fundamentals/Volumes|Volume]]
# [[Technical/DevOps/Kubernetes/Fundamentals/Network|Network]]
Kubernetes became popular due to its advanced application lifecycle management capabilities, implemented through a rich object model, representing different persistent entities in the Kubernetes cluster. Those entities describe:

- What containerized applications we are running.
- The nodes where the containerized applications are deployed.
- Application resource consumption.
- Policies attached to applications, like restart/upgrade policies, fault tolerance, ingress/egress, access control, etc.# ConfigMap