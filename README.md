# Homepage Deployment on Kubernetes

Setup and deployment of the Homepage service.

**URLs**: https://home.cloud.egi.eu (primary) | https://homepage.dyn.cloud.e-infra.cz (legacy)

## Components

- **Ingress**: `homepage-ingress.yaml`
  - Exposes the service publicly on port 443 (HTTPS)
  - TLS/SSL configuration with cert-manager
  - Hosts: `home.cloud.egi.eu` and `homepage.dyn.cloud.e-infra.cz`

- **Deployment**: `homepage-deployment.yaml`
  - Image: `ghcr.io/gethomepage/homepage:latest`
  - Container port: `3000`
  - Security context:
    - `runAsNonRoot: true`
    - `allowPrivilegeEscalation: false`
    - `capabilities.drop: ["ALL"]`
    - `seccompProfile: RuntimeDefault`
  - Volumes:
    - `homepage-config` (ConfigMap) mounted at `/app/config`
      - Read-only configuration files from ConfigMap
    - `logs` (PVC) mounted at `/app/config/logs`
      - Writable logs directory
  - InitContainer: copies required config files from `/app/src/skeleton` to `/app/config` if missing

- **ConfigMap**: `homepage-configmap.yaml`
  - Contains homepage configuration files:
    - `bookmarks.yaml`
    - `services.yaml`
    - `settings.yaml`
    - `widgets.yaml`
  - Updates to ConfigMap require pod restart to take effect.

    ```sh
    kubectl rollout restart deployment homepage
    ```

- **Secret**: `homepage-secret.yaml`
  - Service account token for cluster access.

- **ClusterRole**: `homepage-clusterrole.yaml`
  - Defines cluster permissions for the service account.

- **Service**: `homepage-service.yaml`
  - Exposes the container on port `3000` internally in the cluster.

- **ServiceAccount** and **Secret**
  - Provide required permissions for accessing the cluster.

## Deployment

Apply all resources in the correct order:

```bash
kubectl apply -f homepage-serviceaccount.yaml
kubectl apply -f homepage-clusterrole.yaml
kubectl apply -f homepage-configmap.yaml
kubectl apply -f homepage-secret.yaml
kubectl apply -f homepage-deployment.yaml
kubectl apply -f homepage-service.yaml
kubectl apply -f homepage-ingress.yaml
```

## Accessing the Service

The service is exposed via Ingress at:
- https://home.cloud.egi.eu (primary)
- https://homepage.dyn.cloud.e-infra.cz (legacy)

For local testing or if Ingress is not available, you can port-forward to localhost:

```bash
kubectl port-forward service/homepage 3000:3000 -n namespace
```

Then access Homepage at http://localhost:3000.

## Modifying Homepage Configuration

1. Edit the ConfigMap:

```bash
kubectl edit configmap homepage -n namespace
```

2. After saving changes, restart the pod to pick up the new configuration:

```bash
kubectl delete pod -l app.kubernetes.io/name=homepage -n namespace
```

The pod will recreate with the updated configuration.

## Troubleshooting

If the service doesn't respond:

```bash
# Check pod status
kubectl get pods -n namespace -l app.kubernetes.io/name=homepage

# View pod logs
kubectl logs deployment/homepage -n namespace

# Check Ingress status
kubectl get ingress -n namespace
kubectl describe ingress homepage -n namespace
```
