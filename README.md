# Homepage Dashboard - EGI Cloud

This repository contains the configuration and Helm chart for the EGI Cloud Homepage dashboard.

## Project Structure

- `chart/`: A simplified, local Helm chart for deploying Homepage.
  - `values.yaml`: The primary configuration file where you define all services, widgets, and settings.
  - `templates/configmap.yaml`: Dynamically generates the ConfigMap from `values.yaml`.
  - `templates/deployment.yaml`: Deploys the application with optimized persistent storage and security settings.

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
- **Security**: Pod security contexts are pre-configured to follow cluster restricted policies.

### Installation / Upgrade

To deploy or update the dashboard, run the following command from the repository root:

```bash
helm upgrade --install homepage ./chart -f ./chart/values.yaml -n torre-ns
```

## Advanced Customization

### Adding Files
If you need to add more configuration files (e.g., `docker.yaml`), simply add a new key under the `config:` block in `values.yaml`. The Helm templates will automatically include them and mount them into the application's `/app/config/` directory.

---
*Maintained by the EGI Cloud Team.*
