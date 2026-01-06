# RBAC Roles

Grant least-privilege access with Roles and RoleBindings.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: reader
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
```

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: default
subjects:
  - kind: User
    name: alice
roleRef:
  kind: Role
  name: reader
  apiGroup: rbac.authorization.k8s.io
```

## Notes
- Use `ClusterRole` for cluster-wide permissions.
