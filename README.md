# K8S-Finall
## Overview
This project deploys a secure WordPress application backed by a database on a Kubernetes cluster using **Helm**, and includes a comprehensive monitoring stack via **Prometheus and Grafana** to track cluster resources and container uptimes.

## Architecture & Components
- **WordPress Application:** Deployed via Helm, utilizing persistent storage and private AWS ECR container images.
- **Monitoring Stack (`kube-prometheus-stack`):** Deployed in a dedicated namespace for cluster observability.
- **Grafana Dashboards:** Configured with custom panels tracking container health, phase status (`Running`), and overall cluster pod uptimes.

## Prerequisites
- A running Kubernetes cluster (e.g., Minikube or AWS EC2 K8s node)
- `kubectl` configured and connected to the cluster
- `helm` package manager installed
- AWS CLI configured (if pulling images from AWS ECR)

## Deployment Steps

### 1. Authenticate and Configure AWS ECR Secret (if applicable)
If your container images are hosted on a private AWS ECR registry, ensure your cluster has the proper image pull secret:
```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <your-ecr-registry>
kubectl create secret docker-registry ecr-secret \
  --docker-server=<your-ecr-registry> \
  --docker-username=AWS \
  --docker-password=$(aws ecr get-login-password --region us-east-1) \
  --dry-run=client -o yaml | kubectl apply -f -



2. Deploy the Monitoring Stack
Install Prometheus and Grafana using the community Helm chart:

Bash
helm repo add prometheus-community [https://prometheus-community.github.io/helm-charts](https://prometheus-community.github.io/helm-charts)
helm repo update
helm install kube-prom-stack prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace
3. Deploy WordPress
Install the application using Helm:

Bash
helm install wordpress-deployment bitnami/wordpress -f values.yaml
Accessing Services
To access your services locally or via your cloud instance:

Access WordPress:

Bash
kubectl port-forward svc/wordpress-service 8080:80 --address 0.0.0.0
Open your browser at: http://<your-server-ip>:8080

Access Grafana Dashboard:

Bash
kubectl port-forward svc/kube-prom-stack-grafana 3000:80 --address 0.0.0.0
Open your browser at: http://<your-server-ip>:3000
