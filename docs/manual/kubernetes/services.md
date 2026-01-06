# Services

Stable networking for pods.

## Types
- `ClusterIP`: default, internal.
- `NodePort`: expose on node ports.
- `LoadBalancer`: cloud LB.

## Example
```yaml
apiVersion: v1
kind: Service
metadata:
  name: api
spec:
  type: ClusterIP
  selector:
    app: api
  ports:
    - port: 80
      targetPort: 8080
```
