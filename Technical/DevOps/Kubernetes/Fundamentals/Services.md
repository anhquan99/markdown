# Services
## Problem
- Pods are ephemeral in nature, resources like IP addresses allocated to them can not be static. Pods could be terminated abruptly or rescheduled based on existing requirements.
## Solution
- K8s provides a higher-level abstraction called **Service**, which logically groups Pods and defines a policy to access them.
- These grouping is achieved via **Labels** and **Selectors**.
- Using the Labels and Selectors, a logical grouping is assigned with a name, referred as Service. The Service name is also registered with the cluster's internal DNS service.
![[Pasted image 20240328005603.png]]
## Attribute
- Expose Pods to the Cluster or Externally.
	- Pods have an internal IP by default - it changes when a Pod is replaced.
	- Services group Pods with a shared IP.
	- Services can allow external access to Pods.
- Reaching a Pod from outside the Cluster is not possible at all without Services.
- Services can expose single Pods, ReplicaSets, Deployments, DaemonSets, and StatefulSets. When exposing the Pods managed by a controller, the Service's Selector may use the same label(s) as the controller.
## Example
```YAML
apiVersion: v1  
kind: Service  
metadata:  
  name: frontend-svc  
spec:  
  selector:
	  app: frontend  
  ports:  
  - protocol: TCP    
    port: 80    
    targetPort: 5000
```
![[Pasted image 20240328005948.png]]
- By default, each Service receives an IP address routable only inside the cluster, known as `ClusterIP`.
- The user/client now connects to a Service via its **ClusterIP**, which forwards traffic to one of the Pods attached to it. A Service provides load balancing by default while selecting the Pods for traffic forwarding.
- While the Service forwards traffic to Pods, users can select the `targetPort` on the Pod which receives the traffic. In the example above, the `frontend-svc` Service receives requests from the user/client on port 80 and forward to port 5000 of the Pods (defined `targetPort: 5000`). **If the `targetPort` is not defined explicitly, the traffic will be forwarded to Pods on the port which Service receives traffic**.
- It is very important to ensure that the value of the **targetPort**, which is **5000** in this example, matches the value of the **containerPort** property of the Pod **spec** section.
- Endpoints are created and managed automatically by the Service, not by the Kubernetes cluster administrator.
## Traffic policies
- [[Worker node#Proxy - `kube-proxy`|kube-proxy]] with `iptables` implement the load-balancing mechanism, random by default.
### Options
- **Cluster**: allows `kube-proxy` to target all ready endpoints of the service in the load-balancing process.
- **Local**: isolates the load-balancing process to only include the endpoints of the service located on the same node as the requester Pod. Limited when the service does not have a ready endpoint on the node where the requester Pod is running, the service will not route the request to endpoints on other nodes to satisfy the request.
## Service discovery
- Services are the primary mode of communication between containerized application managed by k8s, it is helpful to be able to discover them at runtime.
### Methods
- **Environment variables**: as soon as the Pod starts on any worker node, the `kubelet` daemon running on that node adds a set of environment variables in the Pod for all active Services
- **DNS**: add-on, which creates a DNS record for each Service (most highly recommended).
## ServiceType
- Access scope is decided by `ServiceType` property:
	- Is only accessible within the cluster.
	- Is accessible from within the cluster and the external world.
	- Maps to an entity which resides either inside or outside the cluster.
### ClusterIP
- The default `ServiceType`.
- A service receives a Virtual IP from its ClusterIP, accessible only from within the cluster.
### NodePort
- A high-port, dynamically picked from the default range **30000-32767**, is mapped to the respective service, from the all the worker nodes.
- For example, if the mapped NodePort is **32233** for the service `frontend-svc`, then, if we connect to any worker node on port **32233**, the node would redirect all the traffic to the assigned ClusterIP - **172.17.0.4**. If we prefer a specific high-port number instead, then we can assign that high-port number to the NodePort from the default range when creating the Service.
  ![[Pasted image 20240328021220.png]]
- Useful when we want to make the service accessible from the external world.
### LoadBalancer
- NodePort and ClusterIP are automatically created, and the external load balancer will route to them.
- The Service is exposed at a static port on each worker node.
- The Service is exposed externally using the underlying cloud provider's load balancer feature.
- The **LoadBalancer** _ServiceType_ will only work if the underlying infrastructure supports the automatic creation of Load Balancers and have the respective support in Kubernetes, as is the case with the Google Cloud Platform and AWS. If no such feature is configured, the LoadBalancer IP address field is not populated, it remains in pending state, but the Service will still work as a typical NodePort type Service.
![[Pasted image 20240328022104.png]]
### ExternalIP
- A Service can be mapped to an **[ExternalIP](https://kubernetes.io/docs/concepts/services-networking/service/#external-ips)** address if it can route to one or more of the worker nodes. Traffic that is ingressed into the cluster with the ExternalIP (as destination IP) on the Service port, gets routed to one of the Service endpoints. This type of service requires an external cloud provider such as Google Cloud Platform or AWS and a Load Balancer configured on the cloud provider's infrastructure.
- ExternalIPs are not managed by Kubernetes.
![[Pasted image 20240328022333.png]]
### ExternalName
- Has no Selectors and does not define any endpoints. When accessed within the cluster, it returns a **CNAME** record of an externally configured Service.
- Make externally configured services available to applications inside the cluster.
### Relationship of services
![[image-19.png]]
## Multi-Port services
- Expose multiple ports at the same time if required.
```YAML
apiVersion: v1  
kind: Service  
metadata:  
  name: my-service  
spec:  
  selector:  
    app: myapp  
  type: NodePort  
  ports:  
  - name: http  
    protocol: TCP  
    port: 8080  
    targetPort: 80  
    nodePort: 31080  
  - name: https  
    protocol: TCP  
    port: 8443  
    targetPort: 443  
    nodePort: 31443
```