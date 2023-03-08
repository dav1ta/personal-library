## replication controller
Replication Controller is the older technology that is being replaced by a ReplicaSet.
ReplicaSet is the new way to setup replication.
automaticaly bring new pods if needed

## Load balancing & scaling

```yml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
 template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
     containers:
     - name: nginx-container
       image: nginx
 replicas: 3
```


`kubectl create -f replica.yml`

`kubectl get replicationcontroller`


```yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec:
 template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
     containers:
     - name: nginx-container
       image: nginx
 replicas: 3
 selector:
   matchLabels:
    type: front-end
```


replica set needs selector definition


## scale replica sets
update file

`kubectl replace -f replica.yml`

`kubectl scale --replicas=6 -f replica.yml`

`kubectl scale --replicas=6 replicaset myapp-replicaset`

## delete replicaset

`kubectl delete replicaset myapp-replicaset`
`kubectl delete --all namespaces`


## get replicasetso
`kubectl get replicasets.apps`

##describe
`kubectl describe replicasets.apps new-replica-set `


## get version of replicaset

`kubectl explain replicaset | grep VERSION`


## edit replica, uses editor automaticaly
`kubectl edit replicaset new-replica-set`
