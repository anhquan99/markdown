# Security Contexts
## Define privilege and access control for Pod/Container
- `userID` and `groupdID`
- Run privileged or unprivileged
- Linux Capabilities
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext: # this is the security context
    #uid = 1000
    runAsUser: 1000
    # gid = 3000
    runAsGroup: 3000
    # groups = 2000
    fsGroup: 2000
    supplementalGroups: [4000]
  volumes:
  - name: sec-ctx-vol
    emptyDir: {}
  containers:
  - name: sec-ctx-demo
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]
    volumeMounts:
    - name: sec-ctx-vol
      mountPath: /data/demo
    securityContext:
      allowPrivilegeEscalation: false
```
## Privileged containers
-  By default, containers run unprivileged.
- Possible to run as privileged to:
	- Acces all devices
	- ...
- Privileged means that container user 0 (root) is directly mapped to host user 0 (root).
- To grant the container this capability, the manifest file of k8s need to have:
```yaml
...
    securityContext:
      privileged: true
...
```
### PrivilegeEscalation
- `allowPrivilegeEscalation` controls whether a process can gain more privileges than its parent process.
- By default k8s allow PrivilegeEscalation.
```yaml
...
    securityContext:
      allowPrivilegeEscalation: true
...
```