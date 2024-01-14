## pods yaml


```yaml
apiVersion: apps/v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
   containers:
   - name: nginx-container
     image: nginx
```

`kubectl create -f pod.yml`


show pods
`kubectl get pods`

show detail info




## create simple yml file

`kubectl run redis --image=redis123 --dry-run=client -o yaml > redis-definition.yaml`

## apply changes

`kubectl apply -f redis-definition.yaml `


## add tolerration to pods

```yaml
apiVersion: apps/v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
   containers:
   - name: nginx-container
     image: nginx

   tolerration:
   -key:app 
   operator: "equal"
   value: blue
   effect: Noschedle | PreferNoSchedule|NoExecute
```


## limit resource

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp-color
  labels:
    name: simple-webapp-color
spec:
 containers:
 - name: simple-webapp-color
   image: simple-webapp-color
   ports:
    - containerPort:  8080
   resources:
     requests:
      memory: "1Gi"
      cpu: "1"
```

## default pods VCPU value is 1 and 512 Mi memory

pods can use more memory that needed but it 
is permanently it will be terminated

## Static pods
if we have only kubelet on server ,but kubelet can create pods.
we can provide kubelet to read pod definition files.

we can add yml files /etc/kubernetes/manifests/ folder
and kubelet automaticly create this pods. if we delete that file. pod will be removed. it works only with Pods.
we can add service parameter to change path
--pod-manifest-path=/home/davit/kubemanifest

or kubeconfig.yaml if it is  not service.

use docker ps to see pods

## use custom sheduler

under spec:
  schedulerName: custom-scheduler

## view events
`kubectl get events`


## get all pods

`kubectl get pods --all-namespaces`

## pod sample

`kubectl run redis --image=redis:alpine --dry-run=client -o yaml > redis.yaml`

## exmpose pod directly NodePort

`kubect run custom-nginx --image=ngin --port=8080`

## expose pod ClusterIp 
`kubectl run httpd --image=httpd:alpine --port=80 --expose`


## get system cluster pods

`kubectl get pods --namespace kube-system`


## filter pods by selector

`kubectl get pods --selector env=dev`


## get all objects using selector

`kubectl get all --selector env=prod`

## multiple selectors

`kubectl get pods --selector env=prod,bu=finance,tier=frontend`

## create yaml from running pod

`kubectl get pod elephant -o yaml > elep.yaml`


## replace pod by force

`kubectl replace -f elephant.yaml --force`

## detect static pods

controlplane at the end of pods name

## get wide info with pods

`kubectl get pods -o wide`


## add arguments to pods


```yml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
 containers:
 - name: ubuntu-sleeper
   image: ubuntu-sleeper
   command: ["sleep2.0"]
   args: ["10"]
```

## add env variables in yml

```yml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp-color
spec:
 containers:
 - name: simple-webapp-color
   image: simple-webapp-color
   ports:
   - containerPort: 8080
   env:
   - name: APP_COLOR
     value: pink
```


## add env with configmaps


`kubectl create configmap app-config --from-literal=APP_COLOR=blue --from-literal=APP_MODE=prod`
 `kubectl create configmap app-config --from-file=app_config.properties (Another way)`

 or 

 ```yaml
apiVersion: v1
kind: ConfigMap
metadata:
 name: app-config
data:
 APP_COLOR: blue
 APP_MODE: prod
 ```


`kubectl get configmaps`


## map configmap

```yml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp-color
spec:
 containers:
 - name: simple-webapp-color
   image: simple-webapp-color
   ports:
   - containerPort: 8080
   envFrom:
   - configMapRef:
       name: app-config
```

## we can inject using volumes too


## use Secrets for passwords

`kubectl create secret generic app-secret --from-literal=DB_Host=mysql --from-literal=DB_User=root --from-literal=DB_Password=paswrd`
`kubectl create secret generic app-secret --from-file=app_secret.properties`


`echo -n "mysql" | base64`


```yml
apiVersion: v1
kind: Secret
metadata:
 name: app-secret
data:
  DB_Host: bX1zcWw=
  DB_User: cm9vdA==
  DB_Password: cGFzd3Jk
```
`kubectl create -f secret-data.yaml`

`kubectl get secrets`
### get data
`kubectl get secret app-secret -o yaml`

### if it is mounted as Volume

each password will we as file like /opt/passwords

## multiple container pods


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp
  labels:
    name: simple-webapp
spec:
  containers:
  - name: simple-webapp
    image: simple-webapp
    ports:
    - ContainerPort: 8080
  - name: log-agent
    image: log-agent
```

