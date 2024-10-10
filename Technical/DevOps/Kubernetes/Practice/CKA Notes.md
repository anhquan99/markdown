# Log location
- System logs: `/var/log`, find folder k8s
- K8s logs: `/var/log/syslog`
- Container logs: `/var/log/containers`
- Take note on:
	- Manifest file
# Administrator credentials
- `/etc/kubernetes/admin.conf`
# Debug tools
- `crictl`: Container Runtime Interface
- `journalctl`
# Log
- Log all container in a deployment use `--all-containers` flag
# Ingress
- To check ingress class you can use `kubectl get ingressclass`.
- Check the NodePort _Service_ for the `Nginx` Ingress Controller to see the ports when see The `Nginx` Ingress Controller has been installed
```
k -n ingress-nginx get svc ingress-nginx-controller
```
# Label selector
- Keyword: `Well-Known Labels, Annotations and Taints`
# Pod priority
- `PriorityClass`
# Certificate
- keyword: `Certificate Management with kubeadm`
# RBAC
1. _Role_ + _RoleBinding_ (available in single _Namespace_, applied in single _Namespace_)
2. _ClusterRole_ + _ClusterRoleBinding_ (available cluster-wide, applied cluster-wide)
3. _ClusterRole_ + _RoleBinding_ (available cluster-wide, applied in single _Namespace_)
4. _Role_ + _ClusterRoleBinding_ (**NOT POSSIBLE**: available in single _Namespace_, applied cluster-wide)
# Generate join command
````yaml
kubeadm token create --print-join-command
````
# Get `kubeadm` version
```plain
apt-cache show kubeadm
```