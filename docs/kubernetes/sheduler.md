## sheduler

### manual schedulin

every yml file pod definiton has nodename
sheduler looks who doesnot have it and runs scheduling
algorithm and binds pod to node

if there is not scheduler pods will be in a pending state

```
spec:
  nodeName: node02
```

we cann not update it after pod creation but we can
update it with post request


### Labels and selectors

Labels are properties attached to items

selectors help to filter labels

```yaml 
 apiVersion: v1
 kind: Pod
 metadata:
  name: simple-webapp
  labels:
    app: App1
    function: Front-end
 spec:
  containers:
  - name: simple-webapp
    image: simple-webapp
    ports:
    - containerPort: 8080
```

`kubectl get pods --selector app=App1`


## to creaate replicaset with connected to pods

```yaml
 apiVersion: apps/v1
 kind: ReplicaSet
 metadata:
   name: simple-webapp
   labels:
     app: App1
     function: Front-end
 spec:
  replicas: 3
  selector:
    matchLabels:
     app: App1
 template:
   metadata:
     labels:
       app: App1
       function: Front-end
   spec:
     containers:
     - name: simple-webapp
       image: simple-webapp   

```

## annotations

annotations:
  buildversion: 1.2
record other details for info 


## taint and tolerations

allow certain nodes to accept only specific pods

`kubectl taint nodes node1 app=blue:Noschedule`


## node affinity

certain pods to exact node


in master taint automaticly is added


`kubectl describe node kubemaster | grep Taint`


### Node selectors
add nodeSelector in pods spec
size: Large


### label nodes

`kubectl label nodes nodename size=Large`

limitation u can not  small or medium


### node affinity

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: myapp-pod
spec:
 containers:
 - name: data-processor
   image: data-processor
 affinity:
   nodeAffinity:
     requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: In
            values: 
            - Large
            - Medium
```


```yaml
apiVersion: v1
kind: Pod
metadata:
 name: myapp-pod
spec:
 containers:
 - name: data-processor
   image: data-processor
 affinity:
   nodeAffinity:
     requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: NotIn
            values: 
            - Small
            ```

## requiredDuringSchedulingIgnoredDuringExecution 

only this type of node

## prefferedDuringSchedulingIgnoredDuringExecution 
if not found sheduler ignore affinity rules


## requiredDuringSchedulingRequirdDuringExecution
bad pods automaticly will be deleted


## taint and toleration together
to place exacly where we want

## Daemon Sets

for example we want logging agent in all cluster and node
or kube-proxy can be deployed as daemon set

```yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: monitoring-daemon
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: monitoring-agent
  template:
    metadata:
     labels:
       app: monitoring-agent
    spec:
      containers:
      - name: monitoring-agent
        image: monitoring-agent
```

`kubectl get daemonset`
`kubectl describe daemonset`

deemon sets uses node affinity and taint after 1.x verion
before was nodeName:


## what we want custom sheduling program.

kubernetes cluster can have multiple shedulers

service options:
--scheduler-name:custom-scheduler

or name int /etc/kubernetes/manifest/kube-sheduler/yaml

and add
in command:
--scheduler-name=custom-scheduler

## if there is multiple replicas sheduler only one is active

there is election process who will be leader

where is parameter to aviod newly created schedulers to get leaders

--lock-object-name=custom-scheduler

## get logs

`kubectl logs custom-scheduler --name-space=kube-system`


## add taint node
`kubectl taint nodes nodes01 spray=mortein:NoSchedule`

## remove taint node

`kubectl taint nodes controlplane node-role.kubernetes.io/master:NoSchedule-`


## add label to node
`kubectl label node node01 color=blue`


## get all daemonsets 
`kubectl get daemonsets --all-namespaces`

## describe daemon shedulet pods
`kubectl describe daemonset kube-proxy --namespace=kube-system`


## create daemonset yaml

`kubectl create deployment elasticsearch --image=k8s.gcr.io/fluentd-elasticsearch:1.20 -n kube-system --dry-run=client -o yaml > fluentd.yaml`

## create addidional scheduler from file


`kubectl create -n kube-system configmap my-scheduler-config --from-file=/root/my-scheduler-config.yaml`

