## CREATE ROLE

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-administrator
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["nodes"]
  verbs: ["get", "list", "delete", "create"]
```

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-role-binding
subjects:
- kind: User
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-administrator
  apiGroup: rbac.authorization.k8s.io
```

## SERVICE ACCOUNT AS BOT ACCOUNT

`kubectl create serviceaccount dashboard-sa`
`kubectl get serviceaccounts`

`kubectl describe serviceaccount dashboiard-sa`

## get secreet
`kubectl describe secret dashboard-sa-token-kbbdm`

## secrets are mounter /var/run/secret/kubernetis.io/serviceaccount

in Pod xml:
  serviceAccount:dashboard-sa
  automountServiceAccountToken: false


## get roles

`kubectl get roles`

## describe role
`kubectl describe role kube-proxy -n kube-system`

## describe rolebinding
`kubectl describe rolebinding kube-proxy -n kube-system`

## use command as different role
`--as dev-user`

## create role 

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "create","delete"]
```

## create rolebind

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```
