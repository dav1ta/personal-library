## cluster cluster

who can acess?
how:
username and password
username and tokens
certificates
externale auth providers - ldap
service accounts

## authorization
RBAC - role based 
ABAC
NODE auth
webhook mode



## TLS sertificates

## between applications

all can access each other but it can be restricted with network policies


## Users

we can not create users in kubernetes but we can  
create service accounts
`kubectl create service account`

## accounts
managed by kube-apiserver
authenticate user

### static file auth

password,user,user_id,group
--basic-auth-file = details.csv

## static token file
token,user,uid, group
--token-auth-file = token.csv

## SSL TLS sertificates
## creting certificates
private key
`openssl genrsa -out ca.key 2048`
specify name of what is for. this is signing
`openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr`
sign request. this is self signed
`openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt`

## generate client certificeates

`openssl genrsa -out admin.key 2048`
this name is for logs mostly
`openssl req -new -key ca.key -subj "/CN=kube-admin" -out admin.csr`
we must mention group details in signing request
`openssl x509 -req -in admin.csr -CA ca.crt -CAkey  ca.key -out admin.crt`


## why we need certs
we cen auth to kluster apiserver using this key
or in cluster definiton we can add this keys

## CA root certificates needed for client

if there is more dns names we have create openssl conf


## user kubelet certificates by its node names
to dermined which node is requested


name:
system:node:node01
system:node:node02

## view certificates
kubeadm automaitlcy deploys certs

`openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout`

## what if new admin comes

CA server - pair of certificates files
certificates key is on CA server.

## certificates API
create CertificateeSigningRequest object
and can be reviewd approved 


## how it is done

`openssl genrsa -out jane.key 204`

`openssl req -new -key jane.key -subj ="/CN=jane" out -jane.csr`

```yml

apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: jane
spec:
  groups:
  - system:authenticated
  usages:
  - digital signature
  - key encipherment
  - server auth
  request:
    <certificate-goes-here>

```

`cat jane.csr |base64 ` > and paster in requet above 

## show request
`kubectl get csr`

## aprove requests

`kubectl certificate approve jane`

it automaticly generates  client certificates

`kubectl get csr jane -o yaml`

`echo cert| base --decode` and share to user

## all the certificates are managed by controller manager

## kubeconfigtes

default config `.kube/config`

- clusters
  - development
  - production
  - google
- contexts
  - admin@production
  - google@production
- Users
  - admin
  - dev

## view config

`kubectl config view`
`kubectl config use-context prod-user@production`
`kubectl config -h`

## config namespaces 
add namespace in config to switch automaticly

##api group

curl http://localhost:6443 -k and add certs

or
kubectl proxy 
it is not kube proxy


## authorization - what they can do

## different types of authorization

- node
- abac
  - can view pod
  - can delete pod
  -  needs policie definiton file
  - it is bad practice
- RBAC
  - we define role
  - associate role to users
- webhook
  - we need auth to be managed to another tool
  - for example open policy agent

- AlwaysAllow
- AllwaysDeny
systemctl - >
--authorization-mode= 


## authorization

### abac

dev-user - access
using policy file in json format.
every time we need change we have to change file and restart server

### RBAC
- create roles and add users to this role

### webhook

openpolycyagent
third party auth

## AlwaysAllow and AllwaysDeny
by default is AlwaysAllow

--authorization-mode=None,RBAC,Webhook




### certificate apprval example
`cat akshay.csr | base64 -w 0`
-w - wrap 0
`---
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  groups:
  - system:authenticated
  request: CCCCCCC
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth``
```

### check status
`kubectl get csr`
### approve csr
`kubectl certificate approve akshay`
### get  detals of cst
`kubectl get csr agent-smith -o yaml`

### deny reject
`kubectl certificate deny agent-smith`

### delete csr
`kubectl delete csr agent-smith`
