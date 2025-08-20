# Admission Control
- Used to specify granular access control policies, which include allowing privileged containers, checking on resource quota, ...
- It comes into effect only after API requests are authenticated and authorized.
- Start API server with `--enable-admission-plugins`
	-  Example: `--enable-admission-plugins=NamespaceLifecycle,ResourceQuota,PodSecurity,DefaultStorageClass`.
## `NodeRestriction`
- Limit the Node labels a `kubelet` can modify.
- Ensure secure workload isolation via labels.