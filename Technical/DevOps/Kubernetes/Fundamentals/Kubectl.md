# Kubectl
## Behind the scenes
1. Master node (control plane), the scheduler analyzes currently running Pods and finds the best Node for the new Pods(s).
2.  The newly created worker node will be moved to 1 current worker node.
3. The `kubelet` manages the Pods and containers (starting, checking health, ...).
## CLI
- `kubectl create deployment --image` create a deployment from image, this image must be in docker hub or in some repository.
## Proxy
- Issuing the `kubectl proxy` command, `kubectl` authenticates with the API server on the control plane node and makes services available on the default proxy port 8001.
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