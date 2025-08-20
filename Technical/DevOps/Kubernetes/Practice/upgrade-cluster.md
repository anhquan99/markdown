# Upgrade Cluster
## Steps
1. First upgrade the master components:
	- `apiserver, controller-manager, scheduler`
2. Then the worker components:
	- `kubelet, kube-proxy`
3. Components same minor version as `apiserver`
	- or 1 version below
## Upgrade a node
1. `kubectl drain`
	- Safely evict all pods from node
	- Mark node as SchedulingDisabled (`kubectl cordon` no need to use this because drain already do this) means no new pod is schedule on this node
2. Do the upgrade
3. `kubectl uncordon`
	- Unmark node as SchedulingDisabled
## How to make your application survive an upgrade
- Pod `gracePeriod` / Terminating state
- Pod Lifecycle Events
- `PodsDisruptionBudget`