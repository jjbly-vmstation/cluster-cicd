# cluster-cicd

**Purpose:**
Centralized CI/CD pipeline repository for the VMStation modular Kubernetes cluster. This repo manages all automation, testing, and deployment pipelines for the entire cluster, running on the masternode (DRONE CI/CD).

---

## 📦 What This Repo Does
- Stores all pipeline definitions (e.g., `.drone.yml`)
- Contains shared pipeline scripts and templates
- Documents CI/CD architecture and integration points
- Coordinates automated deployment, testing, and validation for all modular repos

---

## 🔗 Integration Points
- **Runs on:** masternode (192.168.4.63)
- **Triggers:** On push/PR to any modular repo (via webhook)
- **Automates:**
  - Linting, syntax checks
  - Unit/integration tests
  - Ansible playbook validation
  - Kubernetes manifest validation
  - Automated deployment to cluster
  - Post-deploy validation and notifications

---

## 🗺️ Dependency Graph

```
cluster-cicd (this repo)
    │
    ├── cluster-setup (bootstrap, pre-deploy checks)
    ├── cluster-infra (Kubernetes deployment)
    ├── cluster-config (system config, Ansible)
    ├── cluster-monitor-stack (monitoring)
    ├── cluster-application-stack (apps)
    └── cluster-tools (validation, diagnostics)
         ↑
         └── (feeds results back to cluster-cicd)
```

- All modular repos push to GitHub → triggers pipeline in `cluster-cicd`
- `cluster-cicd` orchestrates build, test, deploy, and validation jobs
- Results and notifications are sent to maintainers

---

## 🚦 Pipeline Workflow (DRONE)

1. **Trigger:** Code pushed to any modular repo
2. **Lint/Test:** Run linting, syntax, and unit tests
3. **Build:** Build/test artifacts if needed
4. **Deploy:**
    - Run Ansible playbooks (cluster-config)
    - Apply manifests (cluster-infra, cluster-application-stack)
    - Deploy monitoring stack (cluster-monitor-stack)
5. **Validate:**
    - Run validation scripts (cluster-tools)
    - Check cluster health, monitoring, and app status
6. **Notify:**
    - Send results to maintainers (email, Slack, etc.)

---

## 📄 Key Files
- `.drone.yml` — Main pipeline definition
- `docs/PIPELINE_ARCHITECTURE.md` — Detailed pipeline and DRONE setup
- `scripts/` — Shared pipeline scripts (future)

---

## 📝 Getting Started
1. Install and configure DRONE on masternode
2. Set up GitHub webhooks to trigger builds
3. Customize `.drone.yml` for your workflow
4. Reference this repo in all modular repo READMEs

---

## 📚 Documentation
- [Pipeline Architecture](docs/PIPELINE_ARCHITECTURE.md)
- [DRONE Setup Guide](docs/DRONE_SETUP.md) (future)

---

## 🏗️ Example Pipeline: `.drone.yml`
See the root of this repo for a sample pipeline file.

---

## 🔒 Security & Secrets
- Store secrets in DRONE’s encrypted secret store
- Never commit secrets to the repo

---

## 🏆 Maintainers
- VMStation Infrastructure Team
