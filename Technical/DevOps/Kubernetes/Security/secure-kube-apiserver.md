# Secure Kuber API server
## `kube-apiserver.yaml`
- `--advertise-address`: address used by the entire cluster. If this variable is not set, it will use the bind-address variable. If that is not set, it will use the default interface of the host.
- `--allow-privileged`: privileged containers are not isolated, they share a namespace with the host, and have near full root privilege on the host, it's default `false`.
- `--authorization-mode`: how every API call is authorized. The default value of Node allows `kubelets` access to various function, then RBAC leverages the `rbac.authorization.k8s.io` API group to determine which HTTPD verbs are allowed on various objects.
- `--client-ca-file`: location of the file to be used for the Certificate Authority. Provisioned by the `certificates.k8s.io` API, which uses a protocol similar to the ACME draft.
- `--enable-admission-plugins`: modern versions of the `kube-apiserver` have admission plugins compiled in. Use this parameter to enable other plugins. You can also use the `–disable-admission-plugins` option to disable compiled in plugins.
- `--enable-bootstrap-token-auth`: if set to true, bootstrapping tokens can be used as bearer token credentials, which allows requests to authenticate against the API server.
```yaml
--etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt  
--etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt  
--etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key  
--etcd-servers=ht‌tps://127.0.0.1:2379  
--kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt  
--kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key  
--kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname  
--proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt  
--proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
```
- The lines starting with `etcd` allow the `kube-apiserver` to communicate with the `etcd` pod, as well as the port and IP address to use.
- The line starting with `kubelet-client` configure the TLS keys used from communication with the cluster `kubelets`. The `--kubelet-preferred-address-types` defines an order of mechanisms by which to determine the address of nodes.
- Should the `kube-apiserver` need to communicate with an Aggregated API (AA) server, the private key file, set by `--proxy-client-key-file` and `--proxy-client-cert-file` are used to configure the TLS connection to the secondary server.
```yaml
--requestheader-allowed-names=front-proxy-client  
--requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt  
--requestheader-extra-headers-prefix=X-Remote-Extra 
--requestheader-group-headers=X-Remote-Group  
--requestheader-username-headers=X-Remote-User  
--secure-port=6443  
--service-account-key-file=/etc/kubernetes/pki/sa.pub  
--service-cluster-ip-range=10.96.0.0/12  
--tls-cert-file=/etc/kubernetes/pki/apiserver.crt  
--tls-private-key-file=/etc/kubernetes/pki/apiserver.key
```
- The lines which start with `--requestheader` configure how the `kube-apiserver` and `kube-proxy` communicate with an aggregator API server.
- The `--secure-port` line sets the port TLS requests will be accepted on. Every API call must clear authentication and authorization before being handled by admission control modules.
- The `--tls` lines set the files used for TLS communication to the `kube-apiserver`.
## Enable audit log
- Auditing is enabled by editing the `kube-apiserver` yaml file.
- `--audit-policu-file`: declares where, inside the container, the policy file is located. You will also need to add a volume and mount point to the container.
- `--audit-log-path`: declares where the logs should be kept inside the container. This will also need a volume and a mount point configured.
- `--audit-log-maxbackup`: determines the maximum number of log files to retain.
- `--audit-log-maxage`: determines how many days the previous logs should be kept.
- `--audit-log-maxsize`: the size in megabytes at which the log file will be rotated.
## Configure API auditing
- When enabled, every API call goes through the audit process, even if eventually excluded by a filter. The audit information is generated by the `kube-apiserver`, so expect the process to consume more memory.
- Every call is divided into three phases from when the call is received (**RequestReceived**), handling the call (**ResponseStarted**), and response call is made (**ResponseComplete**). Should there be an issue, there is also the possibility of a panic (**Panic**). 
	- **None**: Nothing sent to backend.
	- **Metadata**: Metadata, but not request or response body, sent to backend.
	- **Request**: Metadata and request, but not response body sent.
	- **RequestResponse**: Metadata, request, and response bodies sent to backend.
## Audit policy
- While there cloud be a single rule affecting all events, there also could be many rules in a policy file. 
```yaml
apiVersion: audit.k8s.io/v1  
kind: Policy  
rules:  
- level: Metadata  
  resources:  
  - group: ""  
    resources: ["pods/log", "pods/status"]  
- level: Metadata  
  omitStages:  
  - "RequestReceived"
```
## Pod security admission (PSA)
- PSA is a k8s feature that provides a way to enforce security standards for pods in a cluster. It enables administrators to define and enforce security policies that can restrict the behavior and configurations of pods based on their security context.
- Administrators can enforce security policies at the namespace level. Policies can be configured to apply to all pods within a namespace, ensuring consistent security practices.
- There are three predefined policy levels: _Privileged_, _Baseline_, and _Restricted_. These levels offer different degrees of security enforcement, from least restrictive (Privileged), to most restrictive (Restricted).
- There are three predefined enforcement modes:
	1. Audit: Logs a policy violation without affecting the creation of the pod.
	2. Warn: Logs a policy violation and returns a warning to the client, but does not block the pod creation.
	3. Enforce: Blocks the pod creation if it violates the policy.
## Encrypt data at rest
- In order to encrypt secrets at rest, instead of just a symmetrical base64 encoding, the `kube-apiserver` must be updated to use a newly-configured encryption provider configuration.
```yaml
## Example
apiVersion: apiserver.config.k8s.io/v1  
kind: EncryptionConfiguration  
name: configureKeys  
resources:  
  - resources:  
    - secrets  
    providers:  
    - aescbc:  
        keys:  
        - name: encKey  
          secret: Ezqai0SIGChGOwf0VdbkFtYPUM2EYf1TAAQbDrfizJQ=  
    - identity: {}
```