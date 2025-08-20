# `etcd`
## Definition
- `etcd` is a consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.
## Managing tool
- `etcdctl`
### Note
- To request information of `etcd` by using `etcdctl` with certificates:
```bash
# get required certificate path
sudo cat /etc/kubernetes/manifests/etcd.yaml | grep pki
# check health of the etcd
ETCDCTL_API=3 etcdctl --cert /etc/kubernetes/pki/apiserver-etcd-client.crt --key /etc/kubernetes/pki/apiserver-etcd-client.key --cacert /etc/kubernetes/pki/etcd/ca.crt endpoint health
```
- You can read the raw value from `etcd` when create secret from `kubectl`.
```bash
ETCDCTL_API=3 etcdctl --cert /etc/kubernetes/pki/apiserver-etcd-client.crt --key /etc/kubernetes/pki/apiserver-etcd-client.key --cacert /etc/kubernetes/pki/etcd/ca.crt get /registry/secrets/{namespace}/{secret-name} | hexdump -C
```
## Encrypt data at rest
- Using `EncryptionConfiguration`.
- The providers work in order. 
```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources: # resource will be encrypted
      - secrets
      - configmaps
      - pandas.awesome.bears.example
    providers:
      - aescbc: # first provider
          keys:
            - name: key1
              # See the following text for more details about the secret value
              secret: <BASE 64 ENCODED SECRET>
      - identity: {} # second provider
			         # this fallback allows reading unencrypted secrets;
                     # for example, during initial migration
```
- **For writing (encrypting):** Only the **first** provider in the list is ever used.
- **For reading (decrypting):** **All** providers in the list are tried in order until one succeeds.
## References
- https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/