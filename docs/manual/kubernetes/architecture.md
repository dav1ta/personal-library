# Kubernetes Architecture (Concise)

## Control Plane
- **kube-apiserver**: front door for all API requests.
- **etcd**: key-value store for cluster state.
- **controller-manager**: reconciles desired vs actual state.
- **scheduler**: assigns pods to nodes.

## Node Components
- **kubelet**: runs pods on the node and reports status.
- **container runtime**: containerd/CRI-O.
- **kube-proxy**: networking rules for Services.

## Flow
1. You apply YAML to the API server.
2. Controllers reconcile desired state.
3. Scheduler picks a node.
4. Kubelet creates pods.

## Notes
- Treat the API server as the source of truth.
- etcd is critical; back it up.

Next: [Backup](backup.md)
