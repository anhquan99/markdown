# Rollouts
- Argo rollouts is a k8s controller and set of CRDs which provide advanced deployment capabilities.
## Architecture
![](/Image/Pasted%20image%2020260411214635.png)
- Argo rollouts controller monitors the cluster for events and reacts whenever a resource of type `Rollout` is changed.
- When a new version is deployed, the controller creates a new canary replica set. An ingress controller or service mesh is integrated with the controller to split traffic between the existing stable replica set and the new canary pods.