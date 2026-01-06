# Maintenance

## Node Drains
```bash
kubectl drain <node> --ignore-daemonsets --delete-emptydir-data
kubectl uncordon <node>
```

## Upgrades
- Upgrade control plane first, then nodes.
- Keep workloads spread across nodes.

## Housekeeping
- Clean up unused namespaces and CRDs.
- Rotate credentials and certificates.

Next: [Monitoring](monitoring.md)
