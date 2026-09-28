# GitOps Configuration Repository: Quantitative Trading Microservices

<p align="center">
  <img src="./Screenshot 2026-09-17 165818.png" alt="GitOps Architecture Flow" width="850">
</p>

## 📌 Overview
This repository serves as the **Single Source of Truth (Config-Repo)** for the infrastructure, observability stack, and application state of a high-performance quantitative trading platform.

Following a strict **GitOps** methodology, all Kubernetes resources, Helm charts, and environment configurations are stored declaratively. **ArgoCD** continuously monitors this repository and reconciles the live Kubernetes cluster to match this exact state, eliminating configuration drift and manual interventions.

---

## 📂 Repository Structure
The repository is logically separated into application definitions (Helm charts) and GitOps deployment configurations (ArgoCD applications).

```text
.
├── charts/                     # Helm charts for all microservices and stateful apps
│   ├── api-gateway/            # Node.js WebSocket gateway
│   ├── auth-service/           # JWT & Authentication service
│   ├── consumer/               # Event stream consumer
│   ├── execution-engine/       # Core trade execution logic
│   ├── execution-service/      # Trade execution API
│   ├── frontend-service/       # User interface
│   ├── market-data-service/    # Real-time market data ingestion
│   ├── notification-service/   # Alerting and notifications
│   ├── postgres/               # Relational database configuration
│   ├── producer/               # Event stream producer
│   ├── quant-ai-engine/        # Algorithmic analysis and AI models
│   ├── rabbitmq/               # Message broker for async communication
│   └── redis/                  # In-memory caching
├── gitops/                     # ArgoCD declarative application manifests
│   ├── argocd-application.yml  # Multi-app manifest defining all services in `charts/`
│   ├── loki-app.yaml           # Observability: Loki & Promtail stack
│   └── prometheus-app.yaml     # Observability: Kube-Prometheus stack
└── Chart.yaml                  # Umbrella chart definition (Root level)
```

## 🏗️ Architecture & Services
The platform utilizes a highly decoupled, event-driven microservices architecture deployed primarily in the quant namespace.

Edge & UI: Includes api-gateway and frontend-service to handle external client traffic, WebSocket connections, and user interfaces.

Core Quant Logic: Includes quant-ai-engine, execution-engine, execution-service, and market-data-service to ingest real-time market data, run algorithmic strategies, and execute trades.

Event Streaming: Includes rabbitmq, producer, consumer, and notification-service to manage asynchronous communication and reliable message delivery between microservices.

State & Data: Includes postgres and redis to provide persistent relational storage and high-speed in-memory caching.

Observability: Includes prometheus-app and loki-app for centralized metrics logging, alerting, and log aggregation in the monitoring namespace.

## 🚀 Installation & Deployment Guide

### Step 1: Cluster Prerequisites & ArgoCD Installation
Ensure you have a running Kubernetes cluster (e.g., K3s, Minikube, or EKS/GKE) and kubectl configured.

Install the ArgoCD controller into your cluster:
```text 
kubectl create namespace argocd
kubectl apply -n argocd -f [https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml](https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml)
```

Wait for the ArgoCD pods to become ready:
```text
kubectl get pods -n argocd -w
```

### Step 2: Deploy the Observability Stack
Before deploying the microservices, bootstrap the monitoring tools to ensure all application metrics and logs are captured immediately upon startup.

Deploy Prometheus and Loki via ArgoCD:
```text
kubectl apply -f gitops/prometheus-app.yaml -n argocd
kubectl apply -f gitops/loki-app.yaml -n argocd
```
### Note: The Prometheus deployment utilizes ServerSideApply=true to bypass Kubernetes CRD size limitations.

### Step 3: Deploy the Quantitative Microservices
Deploy the entire application suite using the unified ArgoCD application manifest. This file dynamically maps to the configurations stored inside the charts/ directory.
```text
kubectl apply -f gitops/argocd-application.yml -n argocd
```
### Step 4: Verify the Deployment
ArgoCD will automatically begin pulling the Helm charts, creating the quant namespace (if required by the manifests), and spinning up the microservices.

Monitor the synchronization progress:
```text
# Check the status of the ArgoCD Application resources
kubectl get applications -n argocd

# Verify the microservices are running in the quant namespace
kubectl get pods -n quant
```

### Step 5: Accessing the Dashboards
To view live metrics and logs, port-forward the Grafana service to your local machine:
```text
kubectl port-forward svc/kube-prometheus-stack-grafana 8080:80 -n monitoring
```
### Access the dashboard at http://localhost:8080 OR http://YOUR-IP-ADD:8080 (Default username: admin).

## 👨‍💻 Author | DevOps 
### Nishant Mishra
### Computer Science and Engineering
### Passionate about Platform Engineering, GitOps, DevOps, and building resilient distributed systems.
