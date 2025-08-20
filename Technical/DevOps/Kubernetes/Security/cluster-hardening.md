# Cluster Hardening
## Restrict API access
### Request workflow
- API requests are always tied to:
	- A normal user
	- A ServiceAccount
	- Are treated as anonymous requests
- Every request must authenticate or be treated as an anonymous user.
### Restrictions
1. Don't allow anonymous access
2. Close insecure port
3. Don't expose ApiServer to the outside
4. Restrict access from Nodes to API (NodeRestriction)
5. Prevent unauthorized access (RBAC)
6. Prevent pods from accessing API
7. ApiServer port behind firewall / allowed ip ranges (cloud provider)
### Insecure access
- `kube-apiserver --insecure-port=80`
	- HTTP
	- Request bypasses authentication and authorization modules
	- Admission controller still enforces
### Notes
 - In k8s version 1.6+, anonymous access is enabled by default 
	 - If authorization mode other than AlwaysAllow
	 - If ABAC and RBAC require explicit authorization for anonymous
```bash
# manual send api request
curl https://{ip}:6443 --cacert ca --cert crt --key key
```
### Send API request from outside using `kubectl`
1. Expose k8s default service as NodePort.
2. Make the config file available on your machine.
3. Check the DNS on the API server certificate file available at `/etc/kubernetes/pki`. In this file, there are many DNS which the k8s allow to access.
4. Create a mapping in the host file with associate IP and the DNS on your local machine.