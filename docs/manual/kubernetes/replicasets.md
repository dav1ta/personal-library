# ReplicaSets

Ensure a specified number of pod replicas are running.

## Notes
- You usually create ReplicaSets via Deployments.
- Scaling a Deployment updates its ReplicaSet automatically.

```bash
kubectl get rs
kubectl describe rs <name>
```

Next: [Roles](roles.md)
