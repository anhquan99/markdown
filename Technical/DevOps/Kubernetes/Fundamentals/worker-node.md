# Worker Node
- Provides a running env for client application.
- In K8s the application containers are encapsulated in Pods, controlled by the cluster control plane agents running on the control plane node. Pods are scheduled on worker nodes, where they find required compute, memory and storage resources to run, and networking to talk to each other and the outside world.
![[Pasted image 20240324123939.png]]
- A Pod is the smallest scheduling work unit in K8s, is a logical collection of 1 or more containers scheduled together, and the collection can be started, stopped, or rescheduled as a single unit of work.
- In a multi-worker K8s cluster, the network traffic between client users and the containerized applications deployed in Pod is handled directly by the worker nodes, and is not routed through the control plane node.
## Components
- [[worker-node##Container Runtime|Container Runtime]]
- [[worker-node##Node Agent - `kunelet`|Node Agent - kubelet]]
- [[worker-node##Proxy - `kube-proxy`|Proxy - kube-proxy]]
- Add-ons for DNS, Dashboard user interface, cluster-level monitoring and logging
#### Container Runtime
- Because K8s has a lack of capability to directly handle and run containers, it requires a container runtime on the node where a Pod and its containers are to be scheduled.
- Runtimes are required on all nodes of a K8s cluster, both control plane and worker.
- Example of container runtime:
	- `CRI-O`
	- `containerd`
	- `Docker Engine`
	- `rkt`
#### Node Agent - `kubelet`
- Running on each node, control plane and worker.
- Communicates with the control plane.
- It receives Pod definitions, primarily from the [[control-plane##API Server|API Server]], and interacts with the container runtime on the node to run containers associated with the Pod. It also monitors the health and resources of Pods running containers.
- `kubelet` connects to container runtimes through a plugin based interface - the Container Runtime Interface (CRI). In order to connect to interchangeable container runtimes, `kubelete` uses a **CRI shim**, an application which provides a clear abstraction layer between `kubelet` and the container runtime.
![[Pasted image 20240324130822.png]]
###### Liveness and readiness probes
- Allows the `kubelet` to control the health of the application running inside a Pod's container and force container restart of an unresponsive application.
- It is recommended to allow enough time for the readiness probe to possibly fail a few times before a pass, and only then check the liveness probe. If readiness and liveness overlap, there may be a risk that container never reach ready state, while stuck in an infinite re-create-fail lop.
######## Liveness
- Application running inside a container has been running successfully for a while, suddenly stopped responding to the user requests, then that container is no longer useful due to application deadlock or memory pressure.
- Liveness probe checks on an application's health, if it fails the `kubelet` restarts the affected container automatically.
########## Define liveness probe:
- Liveness command
```YAML
apiVersion: v1  
kind: Pod  
metadata:  
  labels:  
    test: liveness  
  name: liveness-exec  
spec:  
  containers:  
  - name: liveness  
    image: k8s.gcr.io/busybox  
    args:  
    - /bin/sh  
    - -c  
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600  
    livenessProbe:  
      exec:  
        command:  
        - cat  
        - /tmp/healthy  
      initialDelaySeconds: 15  
      failureThreshold: 1  
      periodSeconds: 5
```
- Liveness HTTP request
```YAML
...
     livenessProbe:  
       httpGet:  
         path: /healthz  
         port: 8080  
         httpHeaders:  
         - name: X-Custom-Header  
           value: Awesome  
       initialDelaySeconds: 15  
       periodSeconds: 5
...
```
- TCP liveness probe
```YAML
...  
    livenessProbe:  
      tcpSocket:  
        port: 8080  
      initialDelaySeconds: 15  
      periodSeconds: 5  
...
```
######## Readiness
- While initializing, applications have to meet certain conditions before they become ready to server traffic.
```YAML
...  
    readinessProbe:  
          exec:  
            command:  
            - cat  
            - /tmp/healthy  
          initialDelaySeconds: 5   
          periodSeconds: 5  
...
```
#### Proxy - `kube-proxy`
- A daemon - network agent runs on each node, control plane and workers.
- When the [[services]] is removed, `kube-proxy` removes the corresponding iptables rules on all nodes as well.
- Responsible:
	- Watches API server on the master node for the addition, updates, and removal of Services and endpoints.
	- Implementing the Service configuration on behalf of an administrator or developer, in order to enable traffic routing to an exposed application running in Pods.
	- Dynamic updates and maintenance of all networking rules on the node
	- Abstracts the details of Pods networking and forwards connection request (TCP, UDP, and SCTP stream) to the containers in the Pods.
![[Pasted image 20240328012709.png]]

```ad-note
- Imagine a pod as a shipping container. It can hold various individual packages (containers) that are all delivered together to a destination (deployment on a server). The packages might be dependent on each other, like parts for a machine.
- In essence, containers are the building blocks of applications, while pods are the units that Kubernetes manages and schedules for deployment.
```