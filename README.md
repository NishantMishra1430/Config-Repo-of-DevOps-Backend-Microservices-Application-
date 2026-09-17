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
