- The smallest k8s workload object. It is the unit of deployment in k8s, which represents a single instance of the application.
- The smallest "unit" k8s interacts with:
	- Contains and runs one or multiple containers, which they are scheduled together on the same host with the Pod.
	- Pods contain shared resources for all Pod containers.
	- Has a cluster-internal IP by default. Share the same network namespace, meaning that they share a single IP address originally assigned to the Pod.
	  ![[Pasted image 20240327131606.png]]
- Pods are designed to be ephemerals, they do not have the capability to self-heal themselves. That is the reason they are used with controller, which handle Pod's replication, fault tolerance, self-healing, ...
```YAML
# example of pod
apiVersion: v1  
kind: Pod  
metadata:  
  name: nginx-pod  
  labels:  
    run: nginx-pod  
spec:  
  containers:  
  - name: nginx  
    image: nginx:1.22.1  
    ports:  
    - containerPort: 80
```
- `apiVersion` is required, and it must be specified as `v1` for the Pod object definition.
- `kind` is required, and it must be `Pod`, specifying the Pod object type.
- `metdata` holds the object's name and optional labels and annotations.
- `spec` marks the beginning of the block defining the desired state of the Pod object - also named the `PodSpec`. The content of `spec` are evaluated for scheduling purposes, then the `kubelet` of the selected node becomes responsible for running the container image.