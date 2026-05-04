# Architecture
![](/Image/Pasted%20image%2020260409154640.png)
- **UI:** the presentation layer where user interact with ArgoCD
- **Application**
- **Core:** main ArgoCD gitops functionality is implemented by components and Kubernetes controllers from the Core layers.
- **Infra:** represent the tools that ArgoCD depends on as part of its infrastructure.
## Core components
- Argocd-server
- Redis
- Dex: authentication 
- Repo server: retrieve the manifest from the source of truth
- Application controller: reconcile phase is handled
- Notification controller
- Metrics