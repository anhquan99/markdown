## Pod
- The smallest "unit" k8s interacts with:
	- Contains and runs one or multiple containers.
	- Pods contain shared resources for all Pod containers.
	- Has a cluster-internal IP by default.
- Pods are designed to be ephemerals: k8s will start, stop and replce them as needed.
## Deployment
- Control Pod(s).
	- You set a desired state, k8s then changes the actual state.
	- Deployments can be paused, deleted and rolled back.
	- Deployments can be scaled dynamically and automatically.
- So deployments manage a Pod for you, you can also create multiple Deployments, therefor you don't directly control Pods, instead you use deployments to set up desired end state.
- 2 types of deployments:
	- Imperative
	- Declarative
## Service
- Expose Pods to the Cluster or Externally.
	- Pods have an internal IP by default - it changes when a Pod is replaced.
	- Services group Pods with a shared IP.
	- Services can allow external access to Pods.
- Reaching a Pod from outside the Cluster is not possible at all without Services