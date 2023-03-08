## get all services

`kubectl get all --all-namespaces -o yaml > all-deploy.yaml`

## Tools

VELERO

## ETCD cluster backup
--data-dir 
/var/lib/etcd

`etcdctl snapshot save snapshot.db`

`service kube-apiserver stop`

`etcdctl snapshot restore snapshot.db --data-dir /var/lib/etcd-from-backup`

`systemctl daemon-reload`
`systemctl etcd restart`

## etcd need keys for that command
