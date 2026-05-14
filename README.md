# 🛡️ FinShield — Production DevSecOps Ecosystem

FinShield is a secure, high-concurrency stock transaction platform built as a complete DevSecOps Software Development Life Cycle (SDLC) implementation. 

## 🏗️ Architecture & Pipeline Flow

```text
GitHub Push
    │
    ▼
Jenkins (CI/CD)  ◄──── GitHub Webhook (Instant Trigger via ngrok)
    │
    ├── 1. Environment Cleanup (Space Management)
    ├── 2. Secret Ingestion (HashiCorp Vault)
    ├── 3. Automated Testing (Pytest)
    ├── 4. Secure Container Build (Multi-stage Docker)
    ├── 5. Image Vulnerability Scan (Trivy DevSecOps)
    ├── 6. Versioned Artifact Push (Docker Hub)
    └── 7. Orchestration & Scaling (Kubernetes / Minikube)
                                           │
                           ┌───────────────┘
                           ▼
               FinShield API Cluster (2–10 Replicas, HPA Enabled)
                           │
               ┌───────────┴──────────┐
               ▼                      ▼
        PostgreSQL DB          JSON Logs → Logstash → Elasticsearch → Kibana
               │
      HashiCorp Vault (Dynamic Secrets, JWT, API Keys)
```

---

## 📁 Key Features

### 1. Advanced Concurrency Management
- **Atomic Stock Locking**: Implemented using `asyncio.Lock()` to prevent race conditions during unit reservation.
- **10-Minute Hold Window**: Automated background workers (`asyncio.create_task`) manage stock hold timeouts.
- **Session Persistence**: UI state persists across browser refreshes, allowing users to resume transaction windows.

### 2. DevSecOps Security Stack
- **HashiCorp Vault**: Centralized secret management. No plain-text credentials in the repository.
- **Trivy Scanning**: Integrated image vulnerability scanning in the Jenkins pipeline.
- **Network Isolation**: Jenkins-to-K8s bridge via dedicated Docker networks and host mapping.

### 3. Orchestration & Observability
- **Kubernetes HPA**: Automatically scales pods (2 to 10) based on CPU/Memory load.
- **Full ELK Stack**: Logstash ingestion with custom Ruby-based log enrichment (Alert Levels, Performance Classifiers).
- **Kibana Dashboards**: Real-time monitoring of transactions and fraud flags.

---

## 🔧 Service Catalog & Access

| Service | Port | Access URL | Credentials |
|---------|------|------------|-------------|
| 🌐 **App UI (K8s)** | 30000+ | `minikube service list` | — |
| ⚙️ **Jenkins** | 9090 | http://localhost:9090 | Setup on first run |
| 📊 **Kibana** | 5601 | http://localhost:5601 | — |
| 🔐 **Vault UI** | 8200 | http://localhost:8200 | Token: `finshield-root-token` |
| 🔍 **Elasticsearch**| 9200 | http://localhost:9200 | — |

---

## 🚀 Quick Deployment Guide

### 1. Initialize Infrastructure
```bash
# Start Docker Compose (Jenkins, Vault, ELK, Postgres)
docker compose -p finshield-v3 up -d

# Fix Elasticsearch Memory Mapping
sudo sysctl -w vm.max_map_count=262144
```

### 2. Start Kubernetes Cluster
```bash
minikube start --driver=docker
minikube addons enable metrics-server
```

### 3. Trigger CI/CD Pipeline
Simply push your code to the `main` branch. The GitHub Webhook (via ngrok) will instantly trigger the build.

```bash
git add .
git commit -m "feat: updated transaction logic"
git push origin main
```

### 4. Logging Setup (Kibana 8.x)
1. Open http://localhost:5601
2. Go to **Management > Stack Management > Data Views**.
3. Create a data view named `finshield-*`.
4. Go to **Discover** to see your logs flowing in real-time.

---

## 📝 Configuration Management (Ansible)
Located in `devops/ansible`, the playbooks can be used to perform environmental health checks and system configuration before the Jenkins deployment stage.

---

*FinShield — Built for CSE 816 Final Project | Secure Finance Domain DevSecOps Infrastructure*
