# Rollouts
- Argo rollouts is a k8s controller and set of CRDs which provide advanced deployment capabilities.
## Architecture
![](/Image/Pasted%20image%2020260411214635.png)
- Argo rollouts controller monitors the cluster for events and reacts whenever a resource of type `Rollout` is changed.
- When a new version is deployed, the controller creates a new canary replica set. An ingress controller or service mesh is integrated with the controller to split traffic between the existing stable replica set and the new canary pods.
## Deployment patterns
- Reference [Deployment strategy](gitops.md#Deployment%20strategy)
## Analysis
>***Analysis is the capability to connect a Rollout to your metrics provider and define specific thresholds for certain metrics that will decide if an update is successful or not. For each analysis you can define one or more metric queries along with their expected results. A Rollout will progress on its own if metric queries are good, rollback automatically if metrics show failure and pause the rollout if metrics cannot provide a success/failure answer.***
### Template
- A blueprint for health check.
### Run
- Live execution of the template.
### Analysis patterns
- Background analysis is to continuous monitoring that runs parallel to deployment stages.
- Inline analysis is discrete verification checkpoints that must pass before advancing.
- Pre-promotion analysis is to test then switch, validate before user exposure.
- Post-promotion analysis is to switch then verify, validate with real traffic, auto rollback.