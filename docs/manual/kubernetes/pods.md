# Pods

The smallest deployable unit. Usually one container per pod.

## Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo
spec:
  containers:
    - name: app
      image: nginx:latest
      ports:
        - containerPort: 80
```

## Tips
- Use Deployments for most workloads instead of raw Pods.
- Add resource requests/limits.
- Use probes to signal readiness and liveness.
