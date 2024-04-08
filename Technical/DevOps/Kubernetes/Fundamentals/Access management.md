 To access and manage k8s resources or objects in the cluster, we need to access a specific API endpoint on the API server. Each access request goes through the following access control stages:
- [[Authentication]]
- [[Authorization]]
- [[Admission control]]: software modules that validate and/or modify user requests
![[Pasted image 20240327203319.png]]