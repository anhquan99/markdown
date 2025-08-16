# Authorization
- After a successful authentication, users can send the API requests to perform different operations. These request get authorized by k8s using various authorization modules that allow or deny the requests.
## Node
- Node authorization is a special-purpose authorization mode which authorizes API requests made by `kubelet`.
## Attribute-Based Access Control (ABAC)
- K8s grant access to API requests, which combine policies with attributes.
- To enable ABAC mode, users start the API server with the `--authorization-mode=ABAC` and `--authorization-policy-file=PolicyFile.json`.
```JSON
{  
  "apiVersion": "abac.authorization.kubernetes.io/v1beta1",  
  "kind": "Policy",  
  "spec": {  
    "user": "bob",  
    "namespace": "lfs158",  
    "resource": "pods",  
    "readonly": true  
  }  
}
```
## Web hook
- Offload the request authorization to 3rd party services, which return true for successful, or false for failed authorization.
- Start server with `--authorization-webhook-config-file=SOME_FILENAME`.
## Role-Based Access Control (RBAC)
- Regulate the access to resources based on the Roles of individual users.
- Specify what is allowed, everything else is denied.
- Permissions are additive. Example if the user has role which allow get secret and user has cluster role which allow delete secret then the  user has permission to get and delete secret.
- K8s can attach multiple Roles to subjects and restrict resource access by specify operations such as `create, get, update, patch, ...`.
```YAML
apiVersion: rbac.authorization.k8s.io/v1  
kind: Role  
metadata:  
  namespace: lfs158  
  name: pod-reader  
rules:  
- apiGroups: [""] ## "" indicates the core API group  
  resources: ["pods"]  
  verbs: ["get", "watch", "list"]
```
### Resources
- ClusterRole
- Role
- ClusterRoleBinding
- RoleBinding
### Role types
- **Role**: grants access to resources within a specific Namespace.
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```
- **ClusterRole**: grants the same permissions as Role does, but its scope is cluster-wide.
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  # "namespace" omitted since ClusterRoles are not namespaced
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```
### Get resources
```
# get namespaced resource
kubectl api-resources --namespaced=true

# get non namespaced resource
kubectl api-resources --namespaced=false
```
## Role binding
- Apply to all current and future namespaced and non-namespaced resources.
- **RoleBinding**: allows us to bind users to the same namespace as a Role. We could also refer to a ClusterRole in RoleBinding, which would grant permissions to Namespace resources defined in the ClusterRole within the RoleBinding’s Namespace.
```YAML
apiVersion: rbac.authorization.k8s.io/v1  
kind: RoleBinding  
metadata:  
  name: pod-read-access  
  namespace: lfs158  
subjects:  
- kind: User  
  name: bob  
  apiGroup: rbac.authorization.k8s.io  
roleRef:  
  kind: Role  
  name: pod-reader  
  apiGroup: rbac.authorization.k8s.io
```
- **ClusterRoleBinding**: allows us to grant access to resources at a cluster-level and to all Namespaces.
```yaml
apiVersion: rbac.authorization.k8s.io/v1
# This cluster role binding allows anyone in the "manager" group to read secrets in any namespace.
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global
subjects:
- kind: Group
  name: manager # Name is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```
### Combination

|                 | **RoleBinding**                  | **ClusterRoleBinding**     |
| --------------- | -------------------------------- | -------------------------- |
| **Role**        | Available in 1 namespace         | **N/A**                    |
| **ClusterRole** | Available in multiple namespaces | Available in all namespace |
### Verify the user permission
- You can use the `kubectl auth can-i` to verify the user permission