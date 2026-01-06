# Pod Lifecycle

## Phases
- Pending -> Running -> Succeeded/Failed

## Probes
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
```

## Graceful Shutdown
```yaml
terminationGracePeriodSeconds: 30
```

## Notes
- Readiness gates traffic; liveness restarts containers.
