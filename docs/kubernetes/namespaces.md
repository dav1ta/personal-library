## default namespace

kubernetes default uses namespace named default , kubesystem and kubepublic


when service is created dns name automaticly assigned
resources in namespace can refer by its name

`.connect("db-service")`


## to connect database in another namspace
`.connect("db-service.namespace.service.domain.domainlocal")`

`kubectl get pods --namespace=anothername`

## creating pods in namespace

`kubectl create -f pod.yml --namespace=dev`
or add namespace under metadata section in yml


## creating namespaces


```yml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

`kubectl create -f namespace-dev.yaml`
`kubectl create namespace dev`


## set default namespace for command
`kubectl config set-context $(kubectl config current-context) --namespace=dev`
`kubectl get pods`

## show pods in all namespaces
`kubectl get pods --all-namespaces`


## limit resources in namespace using ResourceQuota

```yml
`apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi``

`kubectl create -f quota.yml`
