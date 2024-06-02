- Check health of the database
```shell
kubectl -n kube-system exec -it etcd-cp -- sh \ 
-c "ETCDCTL_API=3 \ #Version to use ETCDCTL_CACERT=/etc/kubernetes/pki/etcd/ca.crt \ ETCDCTL_CERT=/etc/kubernetes/pki/etcd/server.crt \ ETCDCTL_KEY=/etc/kubernetes/pki/etcd/server.key \ etcdctl endpoint health"
```
- Save database snapshot 
```shell
kubectl -n kube-system exec -it etcd-cp -- sh \ -c "ETCDCTL_API=3 \ ETCDCTL_CACERT=/etc/kubernetes/pki/etcd/ca.crt \ ETCDCTL_CERT=/etc/kubernetes/pki/etcd/server.crt \ ETCDCTL_KEY=/etc/kubernetes/pki/etcd/server.key \ etcdctl --endpoints=https://127.0.0.1:2379 snapshot save /var/lib/etcd/snapshot.db "
```
- Check the backup exists in folder `/var/lib/etcd/`
- Files should be backup:
	- `etcd` snapshot
	- `kubeadm-config.yaml`