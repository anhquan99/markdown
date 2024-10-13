# Vim
- Set row number: `set nu`
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
# Opt out of API credential automounting when creating Service Account
- Set the flag `automountServiceAccountToken` to false in yaml file.
- Folder to check `/var/run/secrets/kubernetes.io/serviceaccount/token`.
# Kubelet configuration location
- `/var/lib/kubelet/config.yaml`
- `/etc/kubernetes/kubelet.conf`
## Modify Cluster DNS
1. Modify cluster IP range in `/etc/kubernetes/manifests/kube-apiserver.yaml`
2. Change cluster DNS service `k -n kube-system edit svc kube-dns`
3. Modify Kubelet configuration:
	1. Change cluster DNS in `/var/lib/kubelet/config.yaml`
	2. Edit Kubelet configMap with `k -n kube-system edit cm kubelet-config`
	3. Apply the update to the kubelet configuration immediately on the node 
	   ```
	   kubeadm upgrade node phase kubelet-config
	   systemctl daemon-reload
	   systemctl restart kubelet
	   ```
4. Verify Pod DNS: start a pod, `exec` into that pod and `cat /etc/resolv.conf` is resolve to desired IP  
# etcd
- Find the pod of etcd and then view it information you will get the path of certificate and more.
- After backup etcd when you specify the `--data-dir` in the command, you will need to update the manifest for etcd and tell it get it's data from that restore operation you just did. Search for `/lib/etcd` and replace that with the folder you set when backing up etcd with variable `--data-dir`
# Create a certificate signing request
Keyword `Certificates and Certificate Signing Requests` and `openssl`
# Certificates
```
/etc/kubernetes/pki/apiserver.crt
/etc/kubernetes/pki/apiserver.key

/etc/kubernetes/pki/apiserver-kubelet-client.crt
/etc/kubernetes/pki/apiserver-kubelet-client.key

/var/lib/kubelet/pki/kubelet.crt
/var/lib/kubelet/pki/kubelet.key
```
- Kubernetes uses client certificates to authenticate a client identity to the server and a server certificate to authenticate the server’s identity to the client and establish an encrypted connection.
- In case of `kubelet -> api-server` kubelet needs a client certificate and api-server certificate and for `api-server -> kubelet` then the otherway around.
- Check if a certificate is a server or client one using:
```
openssl x509  -noout -text -in apiserver.crt
```
	- `/etc/kubernetes/pki/apiserver.crt` and `/var/lib/kubelet/pki/kubelet.crt` are server certificates
- `/etc/kubernetes/pki/apiserver-kubelet-client.crt` and `/var/lib/kubelet/pki/kubelet-client-current.pem` are client certificates
## 1. TLS traffic to API server (serving certificates)
`--tls-cert-file, --tls-private-key-file, --tls-sni-cert-key`
## 2. API server communication with other components
### `apiserver` -> `etcd`
`--etcd-cafile, --etcd-certfile, --etcd-keyfile`
### `apiserver` -> `kubelet`
`--kubelet-certificate-authority, --kubelet-client-certificate, --kubelet-client-key`
### `apiserver` -> `aggregated servers`
`--proxy-client-cert-file, --proxy-client-key-file`
## 3. Authentication methods to API server
### x509 client cert auth
`--client-ca-file`
### service account token auth (for verifying service JWT signatures)
`--service-account-key-file`
### auth proxy mTLS verification (for trusting incoming request with user headers)
`--requestheader-client-ca-file`
### oidc trust bundle (for fetching OIDC discovery doc)
`--oidc-ca-file`

# Ports and protocols
## Control plane

|Protocol|Direction|Port Range|Purpose|Used By|
|---|---|---|---|---|
|TCP|Inbound|6443|Kubernetes API server|All|
|TCP|Inbound|2379-2380|etcd server client API|kube-apiserver, etcd|
|TCP|Inbound|10250|Kubelet API|Self, Control plane|
|TCP|Inbound|10259|kube-scheduler|Self|
|TCP|Inbound|10257|kube-controller-manager|Self|
## Worker node(s)

|Protocol|Direction|Port Range|Purpose|Used By|
|---|---|---|---|---|
|TCP|Inbound|10250|Kubelet API|Self, Control plane|
|TCP|Inbound|10256|kube-proxy|Self, Load balancers|
|TCP|Inbound|30000-32767|NodePort Services†|All|
