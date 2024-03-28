# Object model
- Kubernetes became popular due to its advanced application lifecycle management capabilities, implemented through a rich object model, representing different persistent entities in the Kubernetes cluster. Those entities describe:
	- What containerized applications we are running.
	- The nodes where the containerized applications are deployed.
	- Application resource consumption.
	- Policies attached to applications, like restart/upgrade policies, fault tolerance, ingress/egress, access control, etc.
- With each object, we declare our intent, or the desired state of the object, in the **spec** section. The Kubernetes system manages the **status** section for objects, where it records the actual state of the object. At any given point in time, the Kubernetes Control Plane tries to match the object's actual state to the object's desired state. An object definition manifest must include other fields that specify the version of the API we are referencing as the **apiVersion**, the object type as **kind**, and additional data helpful to the cluster or users for accounting purposes - the **metadata**.
- Examples of Kubernetes object types are Nodes, Namespaces, Pods, ReplicaSets, Deployments, DaemonSets, ...
# [[Nodes]]
# [[Pods]]
# [[Namespaces]]
# [[Labels]]
# ReplicationControllers
- No longer a recommended controller because it is a complex operator that ensures a specified number of replicas of Pod is running at any given time, by constantly comparing the actual state with the desired state of the managed application.
- The default recommended controller is the [[Deployments]].
# [[Deployments]]
# [[DeamonSets]]
# [[Services]]
# [[Technical/DevOps/Kubernetes/Fundamentals/Volumes|Volume]]
# [[Technical/DevOps/Kubernetes/Fundamentals/Network|Network]]
Kubernetes became popular due to its advanced application lifecycle management capabilities, implemented through a rich object model, representing different persistent entities in the Kubernetes cluster. Those entities describe:

- What containerized applications we are running.
- The nodes where the containerized applications are deployed.
- Application resource consumption.
- Policies attached to applications, like restart/upgrade policies, fault tolerance, ingress/egress, access control, etc.# ConfigMap
# [[Annotation]]