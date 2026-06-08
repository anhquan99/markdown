# CLI
|Command|Purpose|Example|
|---|---|---|
|`cilium config view`|Display the active Cilium configuration (from the `cilium-config` ConfigMap).|`cilium config view`|
|`cilium config set debug true`|Enable debug logging (patches the ConfigMap and restarts Cilium pods).|`cilium config set debug true`|
|`cilium config set debug false`|Disable debug logging (revert to default logging level).|`cilium config set debug false`|
|`kubectl -n kube-system get pods -l k8s-app=cilium`|Verify Cilium pods and check for restarts after config changes.|`kubectl -n kube-system get pods -l k8s-app=cilium`|
|`kubectl -n kube-system logs <cilium-pod-name>`|Inspect Cilium pod logs (useful when debug is enabled).|`kubectl -n kube-system logs cilium-abcde`
