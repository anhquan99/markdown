- After a successful authentication, users can send the API requests to perform different operations. These request get authorized by k8s using various authorization modules that allow or deny the requests.
# Node
- Node authorization is a special-purpose authorization mode which authorizes API requests made by `kubelet`.
# Attribute-Based Access Control (ABAC)
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
# Webhook
- Offload the request authorization to 3rd party services, which return true for successful, or false for failed authorization.
- Start server with `--authorization-webhook-config-file=SOME_FILENAME`.
# Role-Based Access Control (RBAC)
- Regulate the access to resources based on the Roles of individual users.
- K8s can attach multiple Roles to subjects and restrict resource access by specify operations such as `create, get, update, patch, ...`.
```YAML
apiVersion: rbac.authorization.k8s.io/v1  
kind: Role  
metadata:  
  namespace: lfs158  
  name: pod-reader  
rules:  
- apiGroups: [""] # "" indicates the core API group  
  resources: ["pods"]  
  verbs: ["get", "watch", "list"]
```
## Resources
- ClusterRole
- Role
- ClusterRoleBinding
- RoleBinding
## Role types
- Role: grants access to resources within a specific Namespace.
- ClusterRole: grants the same permissions as Role does, but its scope is cluster-wide.
# Roleb binding
- RoleBinding: allows us to bind users to the same namespace as a Role. We could also refer to a ClusterRole in RoleBinding, which would grant permissions to Namespace resources defined in the ClusterRole within the RoleBinding’s Namespace.
- ClusterRoleBinding: allows us to grant access to resources at a cluster-level and to all Namespaces.
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