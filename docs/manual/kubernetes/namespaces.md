# Namespaces

Logical isolation for resources.

```bash
kubectl create ns staging
kubectl get ns
kubectl config set-context --current --namespace=staging
```

## Tips
- Use namespaces for env separation.
- Enforce limits with ResourceQuotas and LimitRanges.
