# Basic Networking
## Kubernetes networking model
- By default the k8s pod should be able to talk with each other in a cluster even if your nodes are in completely separate networks or data center, ...
- Any application is running on the node should be able to talk to the pod.
![](/image/Pasted%20image%2020260607171024.png)
- K8s does not support networking out of the box, so the users have to deploy networking solution. The networking solution integrate with k8s follows the CNI (Container Networking Interface). Cilium is one of the solutions. 
### Services
- Services abstract the IP of the pods to avoid direct communication with IP address because pods are ephemeral.
- `kube-proxy` manages the IP of the services point to the pods using `iptables`. It runs on every node of the cluster.
![](/image/Pasted%20image%2020260607171426.png)