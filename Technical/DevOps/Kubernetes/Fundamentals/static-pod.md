# Static Pod
- Static pods are managed directly by the kubelet deamon on a specific node, without the intervention from the API server or rest of the k8s cluster components.
- Kubelet works at a pod level and can only understand pods.
## Configuration
- In the `kubelet.service` there is a option `--pod-manifest--path` which point to where the manifests are placed, usually it is `/etc/kubernetes/manifests/`.
- Or define the `staticPodPath` in `kubelet.service` config file.
## Behavior
- The static pod can view by the k8s, but it can not be modified or deleted.
## Usage
- It is used to deploy control plane components.