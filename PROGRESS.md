# Progress Tracker — Kubernetes Bootcamp

## Current Session
**Module 16 - Helm Demo — Stateful App on K8s** (IN PROGRESS)

- **Branch:** `helm-demo-managed-k8s` (feature branch — see `.github/BRANCH-STRATEGY.md`)
- **Cluster:** DigitalOcean Managed Kubernetes (DOKS) — `k8s-helm-demo`
- **kubeconfig:** stored as GitHub Secret `KUBE_CONFIG` (not on disk, not in repo)

### Notes
- CI/CD workflow structure created locally.
- Branch: `helm-demo-managed-k8s`.
- kubeconfig stored as GitHub Secret: `KUBE_CONFIG`.

### Checklist
- [x] CI/CD workflow scaffolded (`.github/workflows/deploy.yml`, triggers on `helm-demo-managed-k8s`)
- [x] Secret-setup instructions documented (`.github/README-secrets.md`)
- [x] Branch strategy documented (`.github/BRANCH-STRATEGY.md`)
- [x] CI/CD workflow created
- [x] KUBE_CONFIG secret instructions documented
- [x] Cluster verification step added to workflow
- [ ] Verify cluster connection confirmed in Actions
- [ ] Add `KUBE_CONFIG` secret in GitHub repo settings
- [ ] Wire up real Helm deploy steps (MongoDB replica set + Mongo Express + NGINX Ingress)
- [ ] Verify deploy run against DOKS cluster `k8s-helm-demo`
- [ ] Merge `helm-demo-managed-k8s` → `k8s` after module is complete

## All Modules

### Completed (Theory)
- [x] Module 1 - Introduction to Kubernetes
- [x] Module 2 - Basic Concepts & K8s Components
- [x] Module 3 - Kubernetes Architecture

### Completed (Lab)
- [x] Module 4 - Minikube & kubectl Local Setup
- [x] Module 5 - kubectl CLI — Main Commands
  - [x] Created nginx Deployment (imperative)
  - [x] Edited Deployment (rolling update)
  - [x] Created MongoDB Deployment
  - [x] Inspected logs of a Pod
  - [x] Got shell of running container (kubectl exec)
  - [x] Deleted deployments
  - [x] Applied configuration file (declarative)
- [x] Module 6 - YAML Configuration Files
- [x] Module 7 - Demo — Deploy MongoDB & Mongo Express
- [x] Module 10 - Ingress
- [x] Module 12 - ConfigMap & Secret Volume Types
- [x] Module 15 - Helm — Package Manager

### In Progress
- [🔄] Module 16 - Helm Demo — Stateful App on K8s (DigitalOcean DOKS via GitHub Actions)

### TODO
- [ ] Module 8 - Namespaces
- [ ] Module 9 - Kubernetes Services
- [ ] Module 11 - Persisting Data with Volumes
- [ ] Module 13 - StatefulSet — Deploying Stateful Apps
- [ ] Module 14 - Managed Kubernetes Services
- [ ] Module 17 - Deploy App from Private Docker Registry
- [ ] Module 18 - Extending K8s API with Operators
- [ ] Module 19 - RBAC — Authorization & Security
- [ ] Module 20 - Microservices in Kubernetes
- [ ] Module 21 - Demo — Deploy Microservices App
- [ ] Module 22 - Production & Security Best Practices
- [ ] Module 23 - Demo — Create Helm Chart for Microservices
- [ ] Module 24 - Demo — Deploy Microservices with Helmfile

---

**Last Updated:** 2026-05-26  
**Total Progress:** 9/24 modules (37.5%)
