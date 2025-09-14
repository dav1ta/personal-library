## 

if node is down 5 minute, it considered as dead
if it will be replicated to another node
pod eviction is 5 minute

## Drain node

`kubectl node drain-1` moves nodes
node becomes unshedulable
reboot
`kubectl uncordon node-1`
`kubectl cordon node-1` -make unshedulable but not move pods




## Vesionin

v1.1.1
major,minor,patch

## upgrade versions of kubernetes
### kubeadm , only cluseter

`kubectl upgrade plan`
`kubectl upgrade apply v1.12.0`

## upgdare master first


## upgrade strategies

### all nodes together

### upgrade one node at time
move pords to another nodes

### create new node with new version

move pods to that and delete old


## take back not for maintenance
`kubectl drain node01 --ignore-daemonsets`
moved pods to another node
now we update that node
`kubectl uncordon node01`

## noschedule but keep apps

`kubectl cordon node01`

## Cluster version
`kubectl get nodes`


## update version in cluster
1. drain nodes
2. upate
3.systemctl restart daemon and kubelet
4. kubectl uncordon

## in node
we need kubeadm upgrade node too

## ETCD backup


`ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 \
 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
 --cert=/etc/kubernetes/pki/etcd/server.crt \
 --key=/etc/kubernetes/pki/etcd/server.key \
 snapshot save /opt/snapshot-pre-boot.db`

## ETCD restore

`ETCDCTL_API=3 etcdctl  --data-dir /var/lib/etcd-from-backup snapshot restore /opt/snapshot-pre-boot.db`
 
 update /etc/kubernetes/manifests/etcd.yaml
 and update volume:hostapath 
 and VolimeMount
## check membrs from external etcd

 `ETCDCTL_API=3 etcdctl \
 --endpoints=https://127.0.0.1:2379 \
 --cacert=/etc/etcd/pki/ca.pem \
 --cert=/etc/etcd/pki/etcd.pem \
 --key=/etc/etcd/pki/etcd-key.pem \
  member list`


## get link for snapshot

`kubectl describe  pods -n kube-system etcd-cluster1-controlplane  | grep advertise-client-urls`

## get all keys
`kubectl describe  pods -n kube-system etcd-cluster1-controlplane  | grep pki`
