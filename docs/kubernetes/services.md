## Services
Services enables communication between various components within and outside of the application.

## Service types

### NodePort
Where the service makes an internal POD accessible on a POD on the NODE. Where the service makes an internal POD accessible on a POD on the NODE. 

```yml
apiVersion: v1
kind: Service
metadata:
 name: myapp-service
spec:
 types: NodePort
 ports:
 - targetPort: 80
   port: 80
   nodePort: 30008
 selector:
   app:myapp
   type:front-end

```
if there is multiple pods in same node it will load balance

else we can access it like ip:port

`kubectl create -f service-definition.yaml`
`kubectl get services`


### CluserIP
in nodes if there is multiple applications like fronted
backend , db they need to communicate to each other.
but communicate with ip is not opation they will change
CluserIP gives ability to group this pods under one name
and give other pods to access with name.


```yml
apiVersion: v1
kind: Service
metadata:
 name: back-end
spec:
 types: ClusterIP
 ports:
 - targetPort: 80
   port: 80
 selector:
   app: myapp
   type: back-end
```


`kubectl get services`

### LoadBalacer
we can instlal load balancer 
like HA or nginx and add node ports
. we can user native cloud balancer 
useing LoadBalacer service. 

```yml
apiVersion: v1
kind: Service
metadata:
 name: back-end
spec:
 types: LoadBalacer
 ports:
 - targetPort: 80
   port: 80
 selector:
   app: myapp
   type: back-end
```

## create sample yaml file

`kubectl run redis --image=redis:alpine --dry-run=client -o yaml > redis.yaml`

## create service from command line

`kubectl expose pod redis --name redis-service --port=3839`
