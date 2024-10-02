# Log location
- System logs: `/var/log`, find folder k8s
- K8s logs: `/var/log/syslog`
- Take note on:
	- Manifest file
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