- In docker, volumes are configured by the user for storing data when container is stopped and removed. But in k8s, user can't run container, k8s run it for the user, so user can't tell container where to store data hence user must tell k8s this.
- K8s can mount volumes into containers.
- Volume lifetime depends on the Pod lifetime.
- Supports many different drivers and types.
- Volumes are not necessarily persistent.
- Volumes survive container restarts and removals.
## Container storage interface (CSI)
- Interface for storing data by third party storage provider.
- CSI is used for generic file storage driver.
## Persistent volumes (PV)
- Node and pod independent.
- The hostPost partially work around that in "one node" environments.
- In the node will have PV claim for PV mapping.
- PV is a standalone cluster resource.
- Can be defined once and used multiple times.
## Storage cluster