# Backup

## What to Back Up
- etcd (cluster state)
- Persistent volumes (application data)
- YAML manifests (Git)

## Approach
- etcd snapshots on a schedule.
- Volume backups via CSI snapshot or backup tool.
- Restore tests on a staging cluster.

Next: [Deployments](deployments.md)
