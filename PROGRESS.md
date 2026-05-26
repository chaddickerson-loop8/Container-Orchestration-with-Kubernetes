# Progress Tracker — Kubernetes Bootcamp

## Current Session
**Module 16 - Helm Demo — Stateful App on K8s** ✅ COMPLETE

**Next up:** Module 17 - Deploy App from Private Docker Registry

- **Branch:** `helm-demo-managed-k8s` (feature branch — see `.github/BRANCH-STRATEGY.md`)
- **Cluster:** DigitalOcean Managed Kubernetes (DOKS) — `k8s-helm-demo`
- **kubeconfig:** stored as GitHub Secret `KUBE_CONFIG` (not on disk, not in repo)

### Notes
- CI/CD workflow structure created locally.
- Branch: `helm-demo-managed-k8s`.
- kubeconfig stored as GitHub Secret: `KUBE_CONFIG`.
- All pipeline screenshots saved to `Screenshots/Module-16/` (per CLAUDE.md screenshot rule — task templates referenced `docs/screenshots/module-16/` but project convention is `Screenshots/`).
- Screenshots placed inline under their matching pipeline steps in `README.md`:
  - `CiCD-K8s_connection.png` → **Verify Cluster Connection (Step 3b)**
  - `Helm-iinstall-cicd-staus-sucess.png` → **Step 3: Helm Setup and Bitnami Repository via CI/CD Pipeline**
  - `deploy MongoDB replica set on DOKS via Helm in CICD.png` → **Step 4: Helm Deployment of MongoDB with Replicas and Secrets**

### Checklist
- [x] CI/CD workflow created (`.github/workflows/deploy.yml`, triggers on `helm-demo-managed-k8s`)
- [x] Branch strategy documented (`.github/BRANCH-STRATEGY.md`)
- [x] KUBE_CONFIG secret instructions documented (`.github/README-secrets.md`)
- [x] KUBE_CONFIG secret configured in GitHub repo settings
- [x] Cluster verification step added (run 26474393325 / commit f57aae9)
- [x] Helm install step activated in pipeline
- [x] Bitnami repository added to pipeline
- [x] Helm repo update added to pipeline
- [x] MongoDB chart verification added to pipeline
- [x] helm-mongodb.yaml values file created
- [x] MongoDB deployed via Helm with replicaset architecture
- [x] 3 replicas configured
- [x] do-block-storage persistence configured
- [x] MongoDB pods verified
- [x] All Kubernetes resources verified
- [x] MongoDB secrets verified
- [x] helm-mongo-express.yaml created
- [x] Mongo Express deployed via kubectl apply
- [x] Mongo Express rollout verified
- [x] Mongo Express logs checked
- [x] MongoDB connection confirmed via Mongo Express
- [x] helm-ingress.yaml created
- [x] NGINX Ingress Controller Helm repo added to pipeline
- [x] NGINX Ingress Controller installed via Helm
- [x] Ingress Controller rollout verified
- [x] LoadBalancer IP assigned by DigitalOcean
- [x] Mongo Express Ingress rule applied
- [x] Ingress verified
- [x] Confirm external browser access to Mongo Express (`http://134.209.140.28` — run 26479921149)
- [x] Add screenshots of successful access
- [x] MongoDB scaled down to 0 replicas — shutdown simulated
- [x] Pod termination verified
- [x] MongoDB scaled back to 3 replicas
- [x] MongoDB rollout verified after scale up
- [x] All pods confirmed running after restore
- [x] All Helm releases listed and verified
- [x] MongoDB Helm release uninstalled
- [x] All 32 pipeline steps documented
- [x] Module 16 summary added to README
- [x] Mark Module 16 COMPLETE
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
- [x] Module 16 - Helm Demo — Stateful App on K8s (DigitalOcean DOKS via GitHub Actions, 32-step pipeline)

### In Progress
- [ ] Module 17 - Deploy App from Private Docker Registry

### TODO
- [ ] Module 8 - Namespaces
- [ ] Module 9 - Kubernetes Services
- [ ] Module 11 - Persisting Data with Volumes
- [ ] Module 13 - StatefulSet — Deploying Stateful Apps
- [ ] Module 14 - Managed Kubernetes Services
- [ ] Module 18 - Extending K8s API with Operators
- [ ] Module 19 - RBAC — Authorization & Security
- [ ] Module 20 - Microservices in Kubernetes
- [ ] Module 21 - Demo — Deploy Microservices App
- [ ] Module 22 - Production & Security Best Practices
- [ ] Module 23 - Demo — Create Helm Chart for Microservices
- [ ] Module 24 - Demo — Deploy Microservices with Helmfile

---

**Last Updated:** 2026-05-26  
**Total Progress:** 10/24 modules (41.7%)
