# Monitoring

## What to Watch
- Node CPU/memory/disk pressure
- Pod restarts and crash loops
- API server latency/errors
- Workload latency and error rates

## Common Stack
- Metrics: Prometheus + Grafana
- Logs: Fluent Bit / Loki / ELK
- Traces: OpenTelemetry

## Quick Checks
```bash
kubectl top nodes
kubectl top pods -A
kubectl get events -A --sort-by=.lastTimestamp
```
