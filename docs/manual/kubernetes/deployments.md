# Deployments

Manage stateless workloads with rolling updates and rollback.

## Example
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
        - name: api
          image: myapp:latest
          ports:
            - containerPort: 8080
```

## Operations
```bash
kubectl rollout status deploy/api
kubectl rollout history deploy/api
kubectl rollout undo deploy/api
```

Next: [Image Security](image-security.md)
