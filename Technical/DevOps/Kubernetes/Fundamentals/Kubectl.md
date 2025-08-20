# `Kubectl`
## Behind the scenes
1. Master node (control plane), the scheduler analyzes currently running Pods and finds the best Node for the new Pods(s).
2.  The newly created worker node will be moved to 1 current worker node.
3. The `kubelet` manages the Pods and containers (starting, checking health, ...).
![[image-22.png]]
## CLI
- `kubectl create deployment --image` create a deployment from image, this image must be in docker hub or in some repository.
## Proxy
- Issuing the `kubectl proxy` command, the K8s create a proxy server between localhost and the K8s API Server.
- Allow to access API locally just over http and without authentication.
![[image-21.png]]
## Port forward
- Forwards connections from a localhost-port to a pod port.
- More generic than `kubectl proxy` and can be used for all TCP traffic, not just HTTP.
## Authentication
- When not using the `kubectl proxy`, we need to authenticate to the API Server when sending API requests.
- We can authenticate by providing a Bearer Token when issuing a `curl` command, or by providing a set of keys and **certificates**.
- Generate token:
```bash
$ export TOKEN=$(kubectl create token default)

$ kubectl create clusterrole api-access-root \  
  --verb=get --non-resource-url=/*

$ kubectl create clusterrolebinding api-access-root \  
  --clusterrole api-access-root --serviceaccount=default:default
# Retrieve the API Server endpoint
$ export APISERVER=$(kubectl config view | grep https | \
  cut -f 2- -d ":" | tr -d " ")
```
- We can extract certificate from the `.kube/config` file, encoded and then passed with `curl` command:
```shell
$ curl $APISERVER --cert encoded-cert --key encoded-key --cacert encoded-ca
```