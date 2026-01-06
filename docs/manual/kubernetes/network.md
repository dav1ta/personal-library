# Networking (Basics)

## Core Ideas
- Every pod gets its own IP.
- Pods can talk to each other directly.
- Services give stable virtual IPs + load balancing.

## Network Policies
Control traffic between pods/namespaces.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
    - Ingress
```

## Notes
- Requires a CNI plugin (Calico, Cilium, etc).
- Policies default to "allow" until you add them.
