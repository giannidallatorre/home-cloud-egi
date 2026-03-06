# Homepage Dashboard - EGI Cloud

This repository contains the configuration and Helm chart for the EGI Cloud Homepage dashboard.

**Production URLs**: 
- [https://home.cloud.egi.eu](https://home.cloud.egi.eu) (Primary)
- [https://homepage.dyn.cloud.e-infra.cz](https://homepage.dyn.cloud.e-infra.cz) (Legacy/Alternative)

## Project Structure

- `chart/`: A simplified, local Helm chart for migrating and managing Homepage.
  - `values.yaml`: The primary configuration file where you define all services, widgets, and settings.
  - `templates/configmap.yaml`: Dynamically generates the ConfigMap from `values.yaml`.
  - `templates/deployment.yaml`: Deploys the application with optimized persistent storage (initContainer + emptyDir) and security settings.

## Getting Started

### Prerequisites

- A Kubernetes cluster.
- Helm installed.
- Proper namespace context (e.g., `torre-ns`).

### Configuration

All configuration is managed within the `chart/values.yaml` file. You can customize:
- **Services**: Define the links and icons for the dashboard.
- **Widgets**: Configure cluster info, search, and resources.
- **Ingress**: Set your hostname and TLS settings.
- **Security**: Pod security contexts are pre-configured to follow cluster `restricted` policies.

### Installation / Upgrade

To deploy or update the dashboard, run the following command from the repository root:

```bash
helm upgrade --install homepage ./chart -f ./chart/values.yaml -n torre-ns
```

## Troubleshooting

If the service doesn't respond or shows a 503 error:

```bash
# Check pod status and restarts
kubectl get pods -n torre-ns -l app.kubernetes.io/name=homepage

# View pod logs (check for EACCES or EROFS errors)
kubectl logs deployment/homepage -n torre-ns

# Check Ingress and Certificate status
kubectl get ingress,certificate -n torre-ns
```

### Manual Configuration Refresh
If changes in `values.yaml` are applied but not reflected, you can force a restart:
```bash
kubectl rollout restart deployment homepage -n torre-ns
```

---
*Maintained by the EGI Cloud Team.*
