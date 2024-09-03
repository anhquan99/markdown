- Argo CD is a GitOps tool implemented as a Kubernetes controller that continuously tracks applications and compares their current state to the target state defined in a Git repository.
- When it detects that a deployed application’s live state deviates from its desired state, it marks the application as out of sync
- Argo CD executes the later GitOps stages and ensures the correct deployment of new configurations to Kubernetes clusters. It is a Kubernetes-native tool for implementing continuous deployment. It differs from external CD tools, which only support push-based deployment because it can pull the updated code from a Git repository and deploy it directly to a Kubernetes resource
![[Pasted image 20240902143901.png]]
# Argo projects
- Argo CD (GitOps controller)
- Argo Rollouts (Progressive Delivery controller)
- Argo Workflows (Workflow engine for Kubernetes)
- Argo Events (Event handling for Kubernetes)
# Sync strategies
- **Manual or automatic sync:** defines what Argo CD does when it finds a new version of your application in Git.
	- Automatic: Argo CD will detect the change then update/create new resources in the cluster.
	- Manual: Argo CD will detect the change but will not change anything in the cluster
- **Auto-pruning of resources:** only applicable for automatic sync. It defines what Argo CD does when you remove/delete files from Git. If enabled, Argo CD will also remove the respective resources in the cluster as well.
- **Self-heal of cluster:** only applicable for automatic sync. It defines what Argo CD does when you make changes directly to the cluster (via `kubectl` or any other way) which will discard the extra changes and bring the cluster back to the described state in Git. 