## IMAGE security

nginx is the same as nginx/nginx

The default registry is docker.io
Google's registry is gcr.io

To login to a private registry:

```bash
docker login private-registry
```

To create a secret for a private registry:

```yaml
kubectl create secret docker-registry regcred \
  --docker-server=private-registry.io \
  --docker-username=registry-user \
  --docker-password=registry-password \
  --docker-email=registry-user@org.com
```

To use the secret in a pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: private-registry.io/apps/internal-app
  imagePullSecrets:
  - name: regcred
```

To run a container as a different user:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: private-registry.io/apps/internal-app
  imagePullSecrets:
  - name: regcred
  securityContext:
    runAsUser: 1000
    capabilities:
      add: ["MAC_ADMIN"]
```

