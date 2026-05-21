

# Container Orchestration with Kubernetes
**DevOps Bootcamp — TechWorld with Nana**

---

## Progress Tracker
- [x] Module 1: Introduction to Kubernetes
- [x] Module 2: Basic Concepts & K8s Components
- [x] Module 3: Kubernetes Architecture
- [x] Module 4: Minikube & kubectl — Local Setup
- [ ] Module 5: kubectl CLI — Main Commands
- [ ] Module 6: YAML Configuration Files
- [ ] Module 7: Demo — Deploy MongoDB & Mongo Express
- [ ] Module 8: Namespaces
- [ ] Module 9: Kubernetes Services
- [ ] Module 10: Ingress
- [ ] Module 11: Persisting Data with Volumes
- [ ] Module 12: ConfigMap & Secret Volume Types
- [ ] Module 13: StatefulSet — Deploying Stateful Apps
- [ ] Module 14: Managed Kubernetes Services
- [ ] Module 15: Helm — Package Manager
- [ ] Module 16: Helm Demo — Stateful App on K8s
- [ ] Module 17: Deploy App from Private Docker Registry
- [ ] Module 18: Extending K8s API with Operators
- [ ] Module 19: RBAC — Authorization & Security
- [ ] Module 20: Microservices in Kubernetes
- [ ] Module 21: Demo — Deploy Microservices App
- [ ] Module 22: Production & Security Best Practices
- [ ] Module 23: Demo — Create Helm Chart for Microservices
- [ ] Module 24: Demo — Deploy Microservices with Helmfile

---

## Module 1: Introduction to Kubernetes
> *What is Kubernetes, why it exists, and the problems it solves.*

<!-- Add notes and commands here -->

---

## Module 2: Basic Concepts & K8s Components
> *Pods, Deployments, Services, ConfigMaps, Secrets, Ingress, Volumes.*

<!-- Add notes and commands here -->

---

## Module 3: Kubernetes Architecture
> *Control plane, worker nodes, API server, etcd, scheduler, kubelet.*

<!-- Add notes and commands here -->

---

## Module 4: Minikube & kubectl — Local Setup
> *Local K8s cluster setup for development and learning.*

### Installation

#### Minikube Installation

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
# Download the latest Minikube binary for Linux x86-64

sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
# Install Minikube to /usr/local/bin and remove the temporary binary
```

**Result:** Successfully installed minikube v1.38.1

#### Cluster Startup

```bash
minikube start
# Start a local Kubernetes cluster using Docker driver
```

**Result:**
- Driver: Docker (running with root privileges)
- Kubernetes v1.35.1 running on Docker 29.2.1
- Addons enabled: storage-provisioner, default-storageclass
- kubectl configured to use minikube cluster and default namespace

#### Cluster Verification

```bash
kubectl get nodes
# List all nodes in the cluster
```

**Output:**
```
NAME       STATUS   ROLES           AGE    VERSION
minikube   Ready    control-plane   4m5s   v1.35.1
```

#### kubectl Installation

See: https://kubernetes.io/docs/tasks/tools/

---

## Module 5: kubectl CLI — Main Commands
> *Core commands for managing K8s resources from the terminal.*

```bash
# Commands will be added as each demo step is completed
```

<!-- Demos: nginx deploy, mongodb deploy, logs, exec, delete, apply config file -->

---

## Module 6: YAML Configuration Files
> *Declarative configuration — the preferred way to manage K8s resources.*

```yaml
# Example YAML files will be added here
```

**Best Practice:** Store config files in Git — either with app code or in a dedicated repo.

---

## Module 7: Demo — Deploy MongoDB & Mongo Express
> *Full demo: Secret → Deployment → Service (internal) → ConfigMap → Deployment → Service (external)*

```bash
# Commands will be added as each step is completed
```

<!-- Steps: MongoDB Deployment, Secret, Internal Service, MongoExpress Deployment, ConfigMap, External Service -->

---

## Module 8: Namespaces
> *Organizing and isolating K8s resources within a cluster.*

```bash
# Commands will be added here
```

---

## Module 9: Kubernetes Services
> *ClusterIP, NodePort, LoadBalancer — and when to use each.*

**Best Practice:** Do NOT use NodePort for external access. Use Ingress or LoadBalancer.

<!-- Add notes and examples here -->

---

## Module 10: Ingress
> *Routing external HTTP/S traffic into the cluster.*

```yaml
# Ingress rules will be added here
```

---

## Module 11: Persisting Data with Volumes
> *PersistentVolume, PersistentVolumeClaim, StorageClass.*

<!-- Add notes and examples here -->

---

## Module 12: ConfigMap & Secret Volume Types
> *Mounting config files and secrets as volumes into pods.*

```bash
# Demo: Mosquitto deployment with ConfigMap and Secret volumes
```

<!-- Steps: Mosquitto deploy, ConfigMap, Secret, updated Deployment with volumes -->

---

## Module 13: StatefulSet — Deploying Stateful Apps
> *Managing stateful applications like databases with stable identities.*

<!-- Add notes and examples here -->

---

## Module 14: Managed Kubernetes Services
> *EKS, GKE, AKS — cloud-managed control planes.*

<!-- Add notes here -->

---

## Module 15: Helm — Package Manager
> *Helm charts, repos, templating, and release management.*

```bash
# Install Helm: https://helm.sh/docs/intro/install/
# Helm Artifact Hub: https://artifacthub.io/
```

---

## Module 16: Helm Demo — Stateful App on K8s
> *Deploy replicated MongoDB + MongoExpress + NGINX Ingress on a cloud cluster.*

```bash
# Commands will be added as each step is completed
```

<!-- Steps: K8s cluster on Linode, MongoDB StatefulSet via Helm, MongoExpress, NGINX Ingress -->

---

## Module 17: Deploy App from Private Docker Registry
> *Pull images from AWS ECR using K8s Secrets.*

```bash
# Commands will be added here
```

<!-- Steps: docker login, create docker config.json, create Secret, configure Deployment -->

---

## Module 18: Extending K8s API with Operators
> *Custom Resource Definitions and Operators for automating complex apps.*

<!-- Add notes here. Operator Hub: https://operatorhub.io/ -->

---

## Module 19: RBAC — Authorization & Security
> *Roles, ClusterRoles, RoleBindings — controlling who can do what in the cluster.*

<!-- Add notes here -->

---

## Module 20: Microservices in Kubernetes
> *Architecture patterns, service mesh concepts (Istio), inter-service communication.*

<!-- Add notes here -->

---

## Module 21: Demo — Deploy Microservices App
> *Deploy an 11-service Online Shop application to a cloud K8s cluster.*

```bash
# Commands will be added here
```

<!-- Steps: YAML manifests, 3-node Linode cluster, Namespace, deploy all services, access from browser -->

---

## Module 22: Production & Security Best Practices
> *7 best practices applied to the microservices config files.*

- [ ] BP1: Pin image versions (no `latest`)
- [ ] BP2: Configure Liveness Probe
- [ ] BP3: Configure Readiness Probe
- [ ] BP4: Set Resource Requests
- [ ] BP5: Set Resource Limits
- [ ] BP6: No NodePort for external services
- [ ] BP7: Run 2+ replicas per Deployment

---

## Module 23: Demo — Create Helm Chart for Microservices
> *Package the microservices app into a reusable Helm chart.*

```bash
# Commands will be added here
```

<!-- Steps: microservices Helm chart, values.yaml per service, redis Helm chart -->

---

## Module 24: Demo — Deploy Microservices with Helmfile
> *Manage multiple Helm chart releases declaratively with Helmfile.*

```bash
# Commands will be added here
```

<!-- Steps: helm install, create Helmfile, install Helmfile, deploy with Helmfile -->

---

## Resources & References

| Resource | Link |
|---|---|
| Minikube Install | https://minikube.sigs.k8s.io/docs/start/ |
| kubectl Install | https://kubernetes.io/docs/tasks/tools/ |
| K8s CLI Cheat Sheet | https://kubernetes.io/docs/reference/kubectl/cheatsheet/ |
| K8s Secrets Best Practices | https://snyk.io/blog/best-practices-for-kubernetes-secrets-management/ |
| Helm Install | https://helm.sh/docs/intro/install/ |
| Helm Artifact Hub | https://artifacthub.io/ |
| Operator Hub | https://operatorhub.io/ |
| RBAC Best Practices | https://rbac.dev/ |
| Config Best Practices | https://kubernetes.io/docs/concepts/configuration/overview/ |
| Kubectx | https://github.com/ahmetb/kubectx#installation |