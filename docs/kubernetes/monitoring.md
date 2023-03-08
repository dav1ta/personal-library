## logging and monitoring


kubelet contains anther tool named **Cadvisor** whhich monitors perfomance

## enable with minikube

`minikube addons enable  metrics-server`

## other

`git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git`

## show stats
`kubectl top node`
`kubectl top pod`

## docker  logs

`docker log -f dockername`

`kubectl logs -f podname`

## if in pods are miltiple container u have to specify name

