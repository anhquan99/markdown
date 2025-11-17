# Schedule Pod
## Taints and tolerations
### Taints
 - The taint is used to prevent a pod to be scheduled in a node.
 - Taints are set on node.
 ```shell
 kubectl taint node node-name key=value:taint-effect
 ```
 - Taint effect:
	 - `NoSchedule`: no new pod is scheduled
	 - `PreferNoSchedule`: try to avoid to schedule
	 - `NoExecute`: no pod is scheduled on this node and all running pods are evicted immediately
### Tolerations
- The toleration is used to schedule a pod to a node which has been tainted.
- Tolerations are set on pod.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "example-key"
    operator: "Exists"
    effect: "NoSchedule"
```

```ad-note
Tains and tolerations do not tell the pod go to a particular node, it tells the node to only accept pods with certain tolerations
```
## Node selector
- Simple form of node selection constraint by querying node labels.
## Node affinity
- More advanced form of node selection constraint with more complex operator but do the same thing as node selector.
- Types:
	- `requiredDuringSchedulingIgnoredDuringExecution`
	- `preferredDuringSchedulingIgnoredDuringExecution`
	- `requiredDuringSchedulingRequiredDuringExecution`
	- `preferredDuringSchedulingRequiredDuringExecution`
- `DuringScheduling` is when the controller is looking for the node to schedule the pod:
	- Required is a hard constraint whereas the pod is not schedule when the all nodes are not matching the criteria
	- Preferred is a soft constraint opposite the required constraint.
- `DuringExecution` is when the pod is running and the node change the label:
	- Required is a hard constraint whereas the pod is evicited when the all nodes are not matching the criteria
	- Preferred is a soft constraint opposite the required constraint.
### Notes
- 