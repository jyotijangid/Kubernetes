1. ETCD backup & restore
```
>>> apt-get update && apt-get install etcd-client
>>> cat /etc/kubernetes/manifests/etcd.yaml
>>> ETCDCTL_API=3 etcdctl --endpoints=127.0.0.1:2379 --cert= --carcert= --key= snapshot save <path-of-etcd-backup-file>

>>> ETCDCTL_API=3 etcdctl snapshot status <path-of-etcd-backup-file>
>>> ETCDCTL_API=3 etcdctl --data-dir </var/lib/etcd-from-backup> snapshot restore <path-of-etcd-backup-file>

- Always update the data-dir **********
- when we are restoring the snapshot we provide a data-dir where the snapshot would be restored & this needs to be updated in data-dir of manifest of etcd but some downtime is observed 
- volume (etcd-data) of etcd-yaml
```