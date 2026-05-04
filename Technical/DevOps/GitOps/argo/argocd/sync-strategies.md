# Sync Strategies
- **Manual or automatic sync:** defines what Argo CD does when it finds a new version of your application in Git.
  - Automatic: Argo CD will detect the change then update/create new resources in the cluster.
  - Manual: Argo CD will detect the change but will not change anything in the cluster
- **Auto-pruning of resources:** only applicable for automatic sync. It defines what Argo CD does when you remove/delete files from Git. If enabled, Argo CD will also remove the respective resources in the cluster as well.
- **Self-heal of cluster:** only applicable for automatic sync. It defines what Argo CD does when you make changes directly to the cluster (via `kubectl` or any other way) which will discard the extra changes and bring the cluster back to the described state in Git.
## Respect ignore differences configs
- Example scenario where the HPA need to scale up or down the pods, ArgoCD allow the replica diff in this situation by ignoring the different (config `ignoreDifferences` and `RespectIgnoreDifferences`).