# Kubernetes Monitoring
## Intension
- Deploy as close to targets as possible.
### Monitor
- Applications running on k8s infra
- Monitor k8s cluster:
	- Control-plane components (`api-server, coredns, kube-scheduler`)
	- `kubelet` (cAdvisor): exposing container metrics
	- `kube-state-metrics`: cluster level metrics (deployments, pods metrics). By default, k8s does not expose metrics so we have to install the `kube-state-metrics` to get metrics from the cluster
	- `node-exporter`: run on all nodes for host related metrics (cpu, memory, network)