# Pipeline Architecture - cluster-cicd

## Overview
This document describes the CI/CD pipeline architecture for the VMStation modular Kubernetes cluster, managed by DRONE on the masternode.

---

## Pipeline Flow

1. **Trigger**: Code pushed to any modular repo (GitHub webhook)
2. **Lint/Test**: Lint YAML, Ansible, and run tests from cluster-tools
3. **Deploy**: Run Ansible playbooks and apply manifests
4. **Validate**: Run cluster-tools validation scripts
5. **Notify**: Send results to maintainers

---

## Integration Points
- **cluster-setup**: Pre-deployment checks
- **cluster-infra**: Kubernetes cluster deployment
- **cluster-config**: System configuration (Ansible)
- **cluster-monitor-stack**: Monitoring stack deployment
- **cluster-application-stack**: Application deployment
- **cluster-tools**: Validation, diagnostics, and test scripts

---

## DRONE Setup (masternode)

1. Install DRONE server and runner on masternode
2. Configure GitHub OAuth and webhooks
3. Store secrets in DRONE’s encrypted store
4. Reference this repo’s `.drone.yml` for pipeline logic

---

## Security
- Use DRONE secrets for credentials and tokens
- Never store secrets in the repository

---

## Maintenance
- Update pipeline as modular repos evolve
- Add new steps for new capabilities (e.g., integration tests, security scans)

---

## Contact
VMStation Infrastructure Team
