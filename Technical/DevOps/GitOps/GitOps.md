# Definition
- GitOps is an operating model pattern, a set of best practice where the entire code delivery process is controlled via Git, including infrastructure and application definition as code and automation to complete updates and rollbacks.
- Natively built for K8s echo system.
- GitOps is a model developed on pre-existing practices.
  ![[Pasted image 20240822115446.png]]
## Principles
- The entire system (infra and apps) is described declaratively
- The canonical desired system state is versioned in Git
- Changes approves are automated and applied to the system
# Challenges with traditional workflow deployments
- Addressing problems can be tricky and the cluster (in K8s) can be modified intentionally or accidentally.
- Does not allow developers to return a cluster to its previous stable state easily.
- Can be difficult to spot these issues or visible but fixing them can be complex ad time-consuming Even after applying a fix, it can be very difficult to understand if the current state of the env matches the desired state.

# GitOps benefits
- GitOps helps by handling the CD step
- Regularly synchronizes the running clusters with the desired target state in the code repo and enhances the security of a cluster by separating permissions for CI and CD tasks
- Regularly compares the cluster state in [[Technical/DevOps/Kubernetes/Kubernetes|Kubernetes]] to the desired state in the manifest files. If someone edits the cluster or deploys pods outside the CI/CD process, the tool detects the changes and can automatically restore the cluster to the target state. Git is the single source of truth, ensuring apps are always in sync
# Operator
- The operator handles CD part, in the image below it is Argo CD.
![[Pasted image 20240822134119.png]]
## Types
- **Cloud infrastructure:** used for infrastructure automation
	- Products: Kubestack, Jenkin X
- **Cluster:** used for application automation
	- Products: Flux, Argo CD
# Use cases
- CD of applications
- CD of cluster resources
- CD of infrastructure
- Detecting/avoiding config drift
- Multi-cluster deployments
# Decision points
1. Where to host git repo?
2. Git repo structure? Level of separation for repos?
3. Runtime (K8s) env structure?
4. Namespace structure? (involved in the FinOps, namespace per env)
5. Operator to use?
6. Sync method?
# Progressive delivery
- Minimum downtime (can be no downtime) and easy roll back.
  ![[Pasted image 20240822135821.png]]
## Blue-green deployments
- Roll out full 100%
![[Pasted image 20240822140034.png]]
## Canary deployments
- With blue-green deployments, if your new version has a hidden issue that manifests itself after some time, then all your users will be affected because the traffic is all or nothing.
- With canary deployments, the traffic will route some percentage so that you can move a subset of users.
![[Pasted image 20240822140502.png]]
# Automated rollbacks with Metrics
- Automate canary deployments, you can set different thresholds that define if a deployment is successful or not.
![[Pasted image 20240822141119.png]]
# Sync strategies (for Argo CD)
- **Manual or automatic sync:** defines what Argo CD does when it finds a new version of your application in Git.
	- Automatic: Argo CD will detect the change then update/create new resources in the cluster.
	- Manual: Argo CD will detect the change but will not change anything in the cluster
- **Auto-pruning of resources:** only applicable for automatic sync. It defines what Argo CD does when you remove/delete files from Git. If enabled, Argo CD will also remove the respective resources in the cluster as well.
- **Self-heal of cluster:** only applicable for automatic sync. It defines what Argo CD does when you make changes directly to the cluster (via `kubectl` or any other way) which will discard the extra changes and bring the cluster back to the described state in Git. 
# Tools
- [[Argo CD]]