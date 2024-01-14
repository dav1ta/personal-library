## Deployment

upgrade pods


```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
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


`kubectl create -f deployment.yml`
`kubectl get deployments`
because it automaticly creates replicasets
`kubectl get replicas`

`kubectl get pods`

`kubectl get all`


## Describe

`kubectl describe deployment name`


## create deployment manually

`kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3`
