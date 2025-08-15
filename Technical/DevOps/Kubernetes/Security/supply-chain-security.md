# Supply Chain Security
## Platform binary
- Every binary of K8s has a hash for user to verify it.
- The hash can be verified by using the `sha512sum`.
## Container binary
- You can find the binary file of the container inside the proc directory of the container and hash it and compare with the authorized hash.
```
# download the server binary of k8s https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.31.md#server-binaries-3
# you can find the k8s api server binary in side the unzip folder
# you can find the binary file of the running api server container by cd into the proc direcroty of the container
ps aux | grep kube-api
find /proc/{process-id}/root/ | grep kube-api
```