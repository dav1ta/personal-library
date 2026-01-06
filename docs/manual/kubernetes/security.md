# Security (Core Practices)

- Use namespaces + RBAC.
- Use NetworkPolicies to limit pod-to-pod access.
- Avoid running containers as root.
- Use read-only root filesystem where possible.
- Rotate credentials and short-lived tokens.

## Pod Security
```yaml
securityContext:
  runAsNonRoot: true
  readOnlyRootFilesystem: true
```
