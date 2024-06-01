- In docker, volumes are configured by the user for storing data when container is stopped and removed. But in k8s, user can't run container, k8s run it for the user, so user can't tell container where to store data hence user must tell k8s this.
- K8s can mount volumes into containers.
- Volume lifetime depends on the Pod lifetime.
- Supports many different drivers and types.
- Volumes are not necessarily persistent.
- Volumes survive container restarts and removals.
# Types
- `emptyDir`: an empty volume is created for Pod as soon as it is scheduled on the worker node. Its life is tightly coupled with the Pod.
- `hostPath`: share a directory between the host and the Pod. If the Pod is terminated, the content of the volume is still available on the host.
- `gcePersistentDisk`: Google Compute Engine (GCE) persistent disk.
- `awsElasticBlockStore`: [[Elastic Compute Cloud (EC2)#Elastic block store (EBS) volume|EBS]].
- `azureDisk`: Azure Data Disk.
- `azureFile`: Azure File Volume.
- `cephfs`.
- `nfs`.
- `iscsi`
- [[Secrets|secret]]: pass sensitive information to Pods.
- [[ConfigMaps|configMap]]: provide configuration data, or shell commands and arguments into a Pod.
- `peristentVolumeClaim`: attach [[Technical/DevOps/Kubernetes/Fundamentals/Volumes#Persistent volumes (PV)|PersistentVolume]] to a Pod using [[Technical/DevOps/Kubernetes/Fundamentals/Volumes#Persistent volume claims (PVC)|persistentVolumeClaim]].
## Container storage interface (CSI)
- Interface for storing data by third party storage provider.
- CSI is used for generic file storage driver.
## Persistent volumes (PV)
- PV is a storage abstraction backed by several storage technologies, provisioned by the cluster administrator.
- Node and pod independent.
- The hostPost partially work around that in "one node" environments.
- In the node will have PV claim for PV mapping.
- PV is a standalone cluster resource.
- Can be defined once and used multiple times.
# Persistent volume claims (PVC)
- PVC is a request for storage by a user. Users request for PersistentVolume resources based on storage class, access mode, size, and optionally volume mode.
- Once a user finishes its work, the attached PersistentVolumes can be released. The underlying PersistentVolumes can then be _reclaimed_ (for an admin to verify and/or aggregate data), _deleted_ (both data and volume are deleted), or _recycled_ for future usage (only data is deleted), based on the configured **persistentVolumeReclaimPolicy** property.
![[Pasted image 20240328111513.png]]
## Modes
- `ReadWriteOnce`: read-write by a single node.
- `ReadOnlyMany`: read-only by many nodes.
- `ReadWriteMany`: read-write by many nodes.
- `ReadWriteOncePod`: read-write by a single pod.
## Storage cluster
## Phases to Persistent Storage
- Provisioning
- Binding
- Using
- Releasing
- Reclaiming