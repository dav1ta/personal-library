# Scheduler

Assigns pods to nodes based on resources, constraints, and policies.

## Common Constraints
- Node selectors / labels
- Taints and tolerations
- Affinity / anti-affinity

```yaml
nodeSelector:
  workload: batch
```

```yaml
tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Next: [Code Review](../programming/code_review.md)
