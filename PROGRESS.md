# Progress Tracker — Kubernetes Bootcamp

## Current Session
**Module 21 - Demo — Deploy Microservices App** (IN PROGRESS)

- **Branch:** `K8s`
- **Scope:** Deploy an 11-service Online Shop microservices app to a K8s cluster (Namespace, deploy all services, browser access).
- **Previous module:** Module 17 ✅ complete (Step 3 `imagePullSecrets` demo documented + Conclusion, commit `5ff9677`)

### Module 17 Checklist
- [x] Pre-existing module-7/12 pods torn down (`kubectl delete deployment/service/configmap/secret/pvc`)
- [x] Clean environment verified — `kubectl get all` shows only `service/kubernetes`
- [x] AWS CLI credentials verified — `aws sts get-caller-identity` → Account `770535378489`
- [x] Docker authenticated with AWS ECR (host-side `docker login` → `Login Succeeded`)
- [x] Host `~/.docker/config.json` observed with `credsStore: "desktop.exe"` (Docker Desktop gotcha documented)
- [x] ECR registry: `770535378489.dkr.ecr.us-east-1.amazonaws.com/my-app`
- [x] ECR token saved to `token.txt` (1793 bytes, gitignored)
- [x] `token.txt` copied to Minikube VM via `minikube cp`
- [x] Minikube SSH verified — `token.txt` readable inside VM
- [x] **In-VM `docker login`** executed using the copied token — produced inline `~/.docker/config.json` inside the VM
- [x] In-VM `config.json` verified — `auths[…].auth` is a real base64 blob (not the host's empty Desktop-wrapped version)
- [x] Navigated to home directory (`cd $HOME` / `pwd` → `/home/loop_8`)
- [x] Docker `config.json` copied from Minikube VM to local host (`minikube cp minikube:/home/docker/.docker/config.json $HOME/.docker/config.json`)
- [x] Host `config.json` now has the inline `auth: …` blob (overwrote the empty Desktop.exe-wrapped version)
- [x] `config.json` base64-encoded — 3312 bytes (PowerShell `[Convert]::ToBase64String(...)` or bash `base64 -w0` — same output)
- [x] `K8S-Config-Files/my-registry-secret.yaml` created (template only — never applied)
- [x] **Secret created via Method 1** — `secret/my-registry-key created` (`kubectl create secret generic … --from-file=.dockerconfigjson=…`)
- [x] **Secret created via Method 2** — `secret/my-registry-key-two created` (`kubectl create secret docker-registry … --docker-password="$TOKEN"`)
- [x] `kubectl get secret` confirmed both Secrets, both `kubernetes.io/dockerconfigjson`, DATA=1
- [x] `kubectl get secret -o yaml` confirmed shape (`.dockerconfigjson` key, base64 payload, `type: kubernetes.io/dockerconfigjson`)
- [x] Step 3: Deploy app using private Docker image — documented failure-vs-success pair (`my-app-deployment.yaml` no `imagePullSecrets` → `ImagePullBackOff`; `my-app-deployment-two.yaml` with `imagePullSecrets: [my-registry-key]` → `Running`) + Conclusion

### Notes
- **Module 17 cluster:** local Minikube (not DOKS — that was Module 16).
- AWS account in use: `770535378489` (verified via `aws sts get-caller-identity`).
- ECR registry: `770535378489.dkr.ecr.us-east-1.amazonaws.com/my-app`.
- Pre-existing pods (mongo-express, mongodb-deployment, mosquitto from earlier modules) were torn down to start with a clean slate before Step 1.
- Module 16 history (kept for reference):
  - CI/CD workflow structure created locally on branch `helm-demo-managed-k8s`.
  - kubeconfig stored as GitHub Secret: `KUBE_CONFIG`.
  - All Module 16 pipeline screenshots in `Screenshots/Module-16/`.
- **Module 17 screenshots:** `Screenshots/Module-17/` (folder pre-created — empty until first capture lands).

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
- [x] Module 17 - Deploy App from Private Docker Registry (AWS ECR auth + `imagePullSecrets` demo)

### In Progress
- [🔄] Module 21 - Demo — Deploy Microservices App (starting)

### TODO
- [ ] Module 8 - Namespaces
- [ ] Module 9 - Kubernetes Services
- [ ] Module 11 - Persisting Data with Volumes
- [ ] Module 13 - StatefulSet — Deploying Stateful Apps
- [ ] Module 14 - Managed Kubernetes Services
- [ ] Module 18 - Extending K8s API with Operators
- [ ] Module 19 - RBAC — Authorization & Security
- [ ] Module 20 - Microservices in Kubernetes
- [ ] Module 22 - Production & Security Best Practices
- [ ] Module 23 - Demo — Create Helm Chart for Microservices
- [ ] Module 24 - Demo — Deploy Microservices with Helmfile

---

**Last Updated:** 2026-06-16  
**Total Progress:** 11/24 modules (45.8%)
