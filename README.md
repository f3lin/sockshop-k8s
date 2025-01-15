# Sockshop Microservices Deployment with ArgoCD, Helm, and NGINX Ingress Controller

This guide explains how to deploy the Sockshop microservices on a Kubernetes cluster using Helm, ArgoCD, and NGINX Ingress Controller.

## Prerequisites
Before proceeding, ensure the following prerequisites are met:

1. A running Kubernetes cluster.
2. `kubectl` is configured to interact with your cluster.
3. `helm` is installed ([Helm Installation Guide](https://helm.sh/docs/intro/install/)).
4. ArgoCD is installed and accessible ([ArgoCD Installation Guide](https://argo-cd.readthedocs.io/en/stable/getting_started/)).
5. NGINX Ingress Controller is installed and configured in your cluster ([NGINX Ingress Controller Guide](https://kubernetes.github.io/ingress-nginx/)).
6. Git repository cloned locally with the project files.

you can also follow my [infra](https://github.com/f3lin/k8s-labo) repository

## Repository Structure
```bash
sockshop-k8s/
├── app
│   ├── Chart.yaml         # Helm chart metadata
│   ├── templates          # Kubernetes manifests for resources
│   └── values.yaml        # Customizable Helm chart values
├── README.md              # Project documentation
└── sockshop-argo-app.yaml # ArgoCD application manifest
```

## Deployment Steps
Follow these steps to deploy the Sockshop microservices:

### 1. Clone the Repository
Clone the Git repository containing the Sockshop project files:

```bash
git clone <repository-url>
cd sockshop-k8s
```

### 2. Install the Helm Chart
Navigate to the `app` directory and install the Helm chart to deploy the Sockshop microservices:

```bash
cd app
helm install sockshop . -n dev --create-namespace
```

This command:
- Installs the Helm chart defined in the `app` directory.
- Creates a namespace `sockshop` if it doesn’t already exist.

### 3. Expose the Application using NGINX Ingress
Ensure your NGINX Ingress Controller is running. Then, add the following Ingress configuration to expose the Sockshop application:

1. Update the `values.yaml` file in the `app` directory with the appropriate ingress settings:
   
   ```yaml
    ingress:
    annotations:
      cert-manager.io/cluster-issuer: "<THE-CLUSTER-ISSUER-NAME>"
    host: <YOUR-RESERVED-DNS-NAME>
    tlsSecretName: '<YOUR-TLS-SECRET-NAME >'
   ```

2. Redeploy the Helm chart to apply the changes:
   ```bash
   helm upgrade sockshop . -n dev
   ```
3. Add a DNS entry (or update `/etc/hosts`) to resolve `<YOUR-RESERVED-DNS-NAME>` to your NGINX ingress controller’s IP address or NGINX ingress dns name you reserved.

### 4. Deploy the ArgoCD Application
The file `sockshop-argo-app.yaml` contains the ArgoCD application manifest. Apply it to register the Sockshop application in ArgoCD:

```bash
kubectl apply -f sockshop-argo-app.yaml -n argocd
```
This will:
- Register the application in ArgoCD.
- Allow ArgoCD to monitor the repository for changes and sync them automatically.

### 5. Access the Application



## Cleaning Up
To remove the deployment, run the following commands:

1. Uninstall the Helm release:
   ```bash
   helm uninstall sockshop -n cev
   ```
2. Delete the namespace:
   ```bash
   kubectl delete namespace dev
   ```
3. Remove the ArgoCD application:
   ```bash
   kubectl delete -f sockshop-argo-app.yaml -n argocd
   ```

## Additional Notes
- Make sure to update the ingress host in `values.yaml` with your actual domain or IP.
- Use SSL/TLS for secure communication. You can configure it using cert-manager or other tools.
