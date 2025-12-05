# VMStation Operator Deployment Checklist

## Phase 0 — Identity & Baseline Bootstrap
1. [ ] Bootstrap FreeIPA and Keycloak SSO
    - [ ] Run FreeIPA container on masternode (or homelab)
    - [ ] Run Keycloak container and integrate with FreeIPA LDAP
    - [ ] Document SSO endpoints and credentials
    - [ ] Validate FreeIPA and Keycloak are reachable before cluster deployment
2. [ ] Enforce baseline OS configuration using Ansible
    - [ ] Run the baseline configuration playbook from `cluster-config/ansible` to automate network, sudoers (no root SSH), time sync, and security hardening
    - [ ] Reference `cluster-config/README.md` and `BASELINE_REPORT.md`

## Phase 1 — Local Development Workspace
- [ ] Clone all organization repos into `vmstation-org` as subfolders (cluster-setup, cluster-config, cluster-monitor-stack, cluster-cicd, etc.)
- [ ] Validate and test Ansible playbooks, manifests, and scripts locally
- [ ] Review per-directory README and `docs/DEPLOYMENT_RUNBOOK.md`

## Phase 2 — Manual Bootstrap on Production Machines
1. [ ] Prepare production machines and repo layout
    - [x] Create `/opt/vmstation-org` (or preferred path)
    - [x] Clone each required repo into matching subfolders
    -   ```bash
        ORG="jjbly-vmstation"
        TARGET="/opt/vmstation-org"
        sudo mkdir -p "$TARGET"
        sudo chown jjbly:jjbly /opt/vmstation-org
        cd "$TARGET"
        for repo in cluster-setup cluster-config cluster-cicd cluster-monitor-stack cluster-application-stack cluster-infra cluster-tools cluster-docs; do
        git clone "https://github.com/$ORG/$repo.git"
        done
        ```
2. [x] Install Ansible
    - [x] Ensure Ansible is installed and available on the production machine before running any playbooks
    -   ```bash
        sudo apt update
        sudo apt install -y ansible
        ```
3. [ ] Use canonical inventory for all playbooks
    - [ ] `vmstation-org/cluster-setup/ansible/inventory/hosts.yml`
4. [ ] Run baseline validation scripts
    - [ ] `cluster-config/scripts/check-drift.sh`
    - [ ] `cluster-config/scripts/gather-config.sh`
5. [ ] Run preflight Ansible playbooks
    - [ ] `cluster-setup/ansible/playbooks/preflight-checks.yml`
6. [ ] Manage secrets and credentials
    - [ ] Use Ansible Vault for playbooks, Kubernetes Secrets for cluster
    - [ ] Ensure monitoring credentials (IPMI for 192.168.4.60) are vaulted
7. [ ] Run main cluster deployment playbooks
    - [ ] `cluster-setup/ansible/playbooks/deploy-cluster.yml` (Kubespray)
8. [ ] Deploy monitoring and infrastructure stacks
    - [ ] `cluster-setup/ansible/playbooks/deploy-monitoring-stack.yml`
    - [ ] Confirm Prometheus, Grafana, Loki, IPMI exporter, directory ownerships, FQDNs for Grafana datasources
9. [ ] Post-deploy validation
    - [ ] `kubectl get nodes`, `kubectl get pods -n kube-system`
    - [ ] Confirm monitoring targets and IPMI metrics
    - [ ] Run post-deploy scripts from `docs/DEPLOYMENT_RUNBOOK.md`
10. [ ] Document and snapshot
    - [ ] Commit cluster-specific config and inventory changes
    - [ ] Record baseline and preflight outputs
11. [ ] Integrate cert-manager with FreeIPA CA
    - [ ] Configure cert-manager issuer to use FreeIPA CA
    - [ ] Automate certificate requests for cluster workloads
    - [ ] Document integration steps and renewal process

## Phase 3 — Transition to kubectl and CI/CD Automation
- [ ] Export resource manifests to cluster-cicd (or GitOps repo)
- [ ] Implement CI/CD pipeline jobs for validation, dry-run, promotion
- [ ] Use cluster-cicd for orchestration, cluster-setup for playbooks/inventory
- [ ] Automate backups, restores, health checks
- [ ] Log and audit all CI/CD deployments

## Pod Lifecycle & Cleanup
- [ ] Ensure workloads are managed by Deployments, StatefulSets, DaemonSets
- [ ] Use update strategies for safe upgrades
- [ ] Investigate and fix CrashLoopBackOff/Error pods
- [ ] Use Jobs with TTL and cleanup logic
- [ ] Use declarative manifests and GitOps for resource cleanup
- [ ] Manual cleanup: `kubectl get pods --all-namespaces -o wide | grep CrashLoopBackOff`, `kubectl delete pod <orphaned-pod>`
- [ ] Design for reconstructability and recovery

## Notes
- Canonical inventory: `cluster-setup/ansible/inventory/hosts.yml`
- Baseline scripts: `cluster-config/scripts/check-drift.sh`, `gather-config.sh`
- Monitoring: Prometheus must scrape IPMI exporter at 192.168.4.60
- UID requirements: Prometheus (65534), Loki (10001), Grafana (472)
- No direct root SSH; use sudo
- Secrets: Ansible Vault and Kubernetes Secrets
- Keep runbooks and documentation current
