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
$ git clone https://github.com/f3lin/sockshop-k8s.git
$ cd sockshop-k8s
```

### 2. Expose the Application using NGINX Ingress

Ensure your NGINX Ingress Controller is running. Then, add the following Ingress configuration to expose the Sockshop application:

1. Update the `values.yaml` file in the `app` directory with the appropriate ingress settings:
   
   ```yaml
    ingress:
    annotations:
      cert-manager.io/cluster-issuer: "<THE-CLUSTER-ISSUER-NAME>"
    host: <YOUR-RESERVED-DNS-NAME>
    tlsSecretName: '<YOUR-TLS-SECRET-NAME >'
   ```
2. Commit and Push your change to your Github Repos:

   ```bash
      $ git add .
      $ git commit "<your message>"
      $ git push origin master
   ```

3. Add a DNS entry (or update `/etc/hosts`) to resolve `<YOUR-RESERVED-DNS-NAME>` to your DNS provider 

### 4. Deploy the ArgoCD Application
The file `sockshop-argo-app.yaml` contains the ArgoCD application manifest. Apply it to register the Sockshop application in ArgoCD:

```bash
$ kubectl apply -f sockshop-argo-app.yaml -n argocd
```

This will:
- Register the application in ArgoCD.
- Allow ArgoCD to monitor the repository for changes and sync them automatically.

### 5. Access the Application

   - URL: `http://<your-argocd-server>`
   - Log in with your ArgoCD credentials.

![Argo Dashboard](https://github.com/f3lin/sockshop-k8s/blob/main/app/argo-dashboard.png "Dashboard")

Sockshop user credential example:
- username: user
- password: password

## Cleaning Up
To remove the deployment, run the following commands:

```bash
$ kubectl delete -f sockshop-argo-app.yaml -n argocd
```

## Additional Notes
- Make sure to update the ingress host in `values.yaml` with your actual domain or IP.
- Use SSL/TLS for secure communication. You can configure it using cert-manager or other tools.
