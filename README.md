# Container Orchestration with Kubernetes
**DevOps Bootcamp — TechWorld with Nana**

---

## Summary

A hands-on learning repo that works through the **24-module Kubernetes section** of the TechWorld with Nana DevOps Bootcamp. Every module's commands, YAML manifests, applied output, and key takeaways live in this single README so the file doubles as a learning log and a personal reference guide.

- **Cluster:** local Minikube (Docker driver, K8s `v1.35.1`) on WSL2 / Ubuntu 24.04
- **Manifests:** all YAML lives in [`K8S-Config-Files/`](./K8S-Config-Files) and is applied with `kubectl apply -f`
- **Pattern per module:** intro → commands → output → key learnings → **Summary** → **Conclusion**
- **Goal:** by Module 24, be able to deploy, expose, configure, secure, and operate microservices on Kubernetes — locally and in production-style setups (Helm, Helmfile, RBAC, Operators)

Jump to the [Progress Tracker](#progress-tracker) below to see which modules are complete.

---

## Project Structure
```
.
├── README.md              # All module notes, commands, and examples
├── CLAUDE.md              # Claude assistant instructions
├── PROGRESS.md            # Progress notes
├── K8S-Config-Files/      # All Kubernetes manifests (apply with -f K8S-Config-Files/<file>.yaml)
│   ├── nginx-deployment.yaml
│   ├── nginx-service.yaml
│   ├── mongo-secret.yaml
│   ├── mongo.yaml
│   ├── mongo-configmap.yaml
│   └── mongo-express.yaml
└── documents/             # Reference documents
```

---

## Progress Tracker
- [ ] Module 1: Introduction to Kubernetes
- [ ] Module 2: Basic Concepts & K8s Components
- [ ] Module 3: Kubernetes Architecture
- [ ] Module 4: Minikube & kubectl — Local Setup
- [ ] Module 5: kubectl CLI — Main Commands
- [x] Module 6: YAML Configuration Files
- [x] Module 7: Demo — Deploy MongoDB & Mongo Express
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

### Summary
Installed Minikube + kubectl and started a local single-node Kubernetes cluster using the Docker driver. Verified the cluster with `kubectl get nodes` (single control-plane node, `Ready`, v1.35.1).

### Conclusion
Local K8s sandbox is live. Every subsequent module (5–24) runs against this Minikube cluster — no cloud account needed. **Gotcha to remember:** because Minikube was started with `sudo` (Docker driver), the profile lives under root and the user's `minikube` CLI can't see it without sudo; `kubectl` works fine either way.

---

## Module 5: kubectl CLI — Main Commands
> *Core commands for managing K8s resources from the terminal.*

### Inspect Cluster & Verify Setup

```bash
kubectl get nodes
# List all worker and control-plane nodes
```

```bash
kubectl get svc
# List all services (kubernetes service exposes cluster API)
```

### Create Deployments Imperatively

```bash
kubectl create deployment nginx-deploy --image=nginx
# Create a deployment directly from command line
```

### Get Resources

```bash
kubectl get deployment
# List all deployments with replica status
```

```bash
kubectl get pod
# List all pods with status and age
```

```bash
kubectl get replicaset
# List replica sets managing pod replicas
```

### Update Deployments

```bash
kubectl set image deployment/nginx-deploy nginx=nginx:1.27.0
# Update container image in a deployment (triggers rolling update)
```

```bash
kubectl edit deployment nginx-deploy
# Edit deployment YAML in default editor ($EDITOR)
```

### Inspect Pod Details

```bash
kubectl describe pod mongo-deployment-5dc7f4b7d7-rx6s8
# Show full pod details: events, status, resource requests, volumes
```

```bash
kubectl logs nginx-deploy-696bf5ffff-bptwj
# Show container standard output and error logs
```

### Execute Commands in Pods

```bash
kubectl exec -it mongo-deployment-5dc7f4b7d7-rx6s8 -- bin/bash
# Open interactive shell inside a running container (debugging)
```

### Delete Resources

```bash
kubectl delete deployment mongo-deployment
# Delete a deployment (automatically removes pods and replica sets)
```

### Apply Declarative Configuration

```bash
kubectl apply -f K8S-Config-Files/nginx-deployment.yaml
# Create or update resources from YAML file (idempotent)
```

**nginx-deployment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
```

### Key Learnings

- **Imperative:** `kubectl create deployment` — quick for testing
- **Declarative:** `kubectl apply -f yaml` — preferred for production (version controlled, repeatable)
- **Rolling Updates:** `kubectl set image` or `kubectl edit` — zero-downtime deployment updates
- **Debugging:** `kubectl logs` and `kubectl exec` — troubleshoot pod issues
- **BP1:** Always pin image versions (nginx:1.25 not nginx)

### Summary
Covered the core kubectl verbs for the full resource lifecycle: **inspect** (`get`, `describe`), **create** (imperative `create` vs declarative `apply -f`), **update** (`set image`, `edit`), **debug** (`logs`, `exec -it`), and **delete**. Saw rolling updates triggered by image changes and used `exec` to shell into a running pod.

### Conclusion
`kubectl` is the universal interface to any K8s cluster — local Minikube or production EKS/GKE/AKS. The declarative `apply -f` workflow is the production standard because YAML lives in Git (versioned, repeatable, code-reviewable). Imperative commands are best kept to learning and ad-hoc debugging. Module 6 builds directly on this by going YAML-only.

---

## Module 6: YAML Configuration Files
> *Declarative configuration — the preferred way to manage K8s resources.*

### nginx-deployment.yaml
Defines a Deployment running 2 replicas of `nginx:1.16` exposing container port `8080`. Uses label `app: nginx` so the Service can select these pods.

### nginx-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx        # Matches pods labeled app=nginx
  ports:
    - protocol: TCP
      port: 80        # Service port
      targetPort: 8080 # Container port on selected pods
```

### Apply & Inspect
```bash
kubectl apply -f K8S-Config-Files/nginx-deployment.yaml
kubectl apply -f K8S-Config-Files/nginx-service.yaml
kubectl describe service nginx-service   # Shows selector, endpoints, ports
kubectl get pod -o wide                  # Shows pod IPs + node assignment
kubectl delete -f K8S-Config-Files/nginx-service.yaml
kubectl delete -f K8S-Config-Files/nginx-deployment.yaml
```

### Key Output
```
Selector:     app=nginx
Type:         ClusterIP
IP:           10.104.232.155
Port:         80/TCP
TargetPort:   8080/TCP
Endpoints:    10.244.0.6:8080,10.244.0.7:8080
```
Endpoints are auto-populated when pod labels match the Service `selector`.

**Best Practice:** Store config files in Git — either with app code or in a dedicated repo.

### Summary
Wrote paired manifests — `nginx-deployment.yaml` (2 replicas, label `app: nginx`) and `nginx-service.yaml` (selects `app: nginx`, routes service port `80` → container port `8080`). Applied both, confirmed Service `Endpoints` were auto-populated with both pod IPs, then cleaned up with `kubectl delete -f`.

### Conclusion
YAML manifests are the unit of Kubernetes configuration: declarative, source-controllable, and idempotent under `kubectl apply`. **Label/selector matching is the connective tissue** of K8s — it's how a Service finds its Pods, how a Deployment finds its ReplicaSet, and how a ReplicaSet finds its Pods. Module 7 stacks five different manifest kinds (Secret, ConfigMap, Deployment, Service ×2) into one working app and depends on this label-matching pattern throughout.

---

## Module 7: Demo — Deploy MongoDB & Mongo Express
> *Full demo: Secret → Deployment → Service (internal) → ConfigMap → Deployment → Service (external)*

### Step 1: MongoDB Secret (`mongo-secret.yaml`)

Stores MongoDB root credentials as Base64-encoded values so they aren't plaintext in the Deployment manifest.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongodb-secret
type: Opaque
data:
  mongo-root-username: dXNlcm5hbWU=   # 'username' base64-encoded
  mongo-root-password: cGFzc3dvcmQ=   # 'password' base64-encoded
```

**Encode your own values:**
```bash
echo -n 'username' | base64
echo -n 'password' | base64
```

**Apply order matters** — Secret must exist before the Deployment that references it.

```bash
kubectl apply -f K8S-Config-Files/mongo-secret.yaml
kubectl get secret
```

**Output:**
```
NAME             TYPE     DATA   AGE
mongodb-secret   Opaque   2      3m10s
```

### Step 2: MongoDB Deployment (`mongo.yaml`)

Single MongoDB pod with root credentials injected from a Kubernetes **Secret** (`mongodb-secret`).

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
```

**Key points:**
- `image: mongo` — official MongoDB image from Docker Hub
- `containerPort: 27017` — MongoDB's default port
- Credentials pulled from `mongodb-secret` via `secretKeyRef` (never plaintext in YAML)
- Requires `mongodb-secret` to exist **before** applying this deployment

**Apply & verify:**
```bash
kubectl apply -f K8S-Config-Files/mongo.yaml
kubectl get all
```

**Output:**
```
NAME                                     READY   STATUS    RESTARTS   AGE
pod/mongodb-deployment-df5cd6568-tb9dl   1/1     Running   0          15s

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mongodb-deployment   1/1     1            1           15s

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/mongodb-deployment-df5cd6568   1         1         1       15s
```

**Inspect the pod:**
```bash
kubectl describe pod mongodb-deployment-df5cd6568-tb9dl
```

**Key output (trimmed):**
```
Status:    Running
IP:        10.244.0.8
Containers:
  mongodb:
    Image:   mongo
    Port:    27017/TCP
    State:   Running
    Ready:   True
    Environment:
      MONGO_INITDB_ROOT_USERNAME: <set to the key 'mongo-root-username' in secret 'mongodb-secret'>
      MONGO_INITDB_ROOT_PASSWORD: <set to the key 'mongo-root-password' in secret 'mongodb-secret'>
Events:
  Normal  Scheduled  default-scheduler  Successfully assigned default/mongodb-deployment-... to minikube
  Normal  Pulled     kubelet            Successfully pulled image "mongo"
  Normal  Started    kubelet            Container started
```

Confirms env vars are wired to the Secret keys and the pod is healthy.

### Step 3: MongoDB Internal Service (appended to `mongo.yaml`)

A **ClusterIP** Service (default type) so Mongo Express can reach MongoDB inside the cluster. Appended to `mongo.yaml` as a second YAML document, separated by `---`.

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb        # Matches pods with label app=mongodb
  ports:
  - protocol: TCP
    port: 27017         # Service port
    targetPort: 27017   # Container port on selected pods
```

**Key points:**
- No `type:` field → defaults to **ClusterIP** (internal-only)
- `selector: app: mongodb` matches the Deployment's pod labels
- Mongo Express will connect via DNS name `mongodb-service` (port 27017)

**Apply & verify:**
```bash
kubectl apply -f K8S-Config-Files/mongo.yaml   # Re-applies Deployment + creates Service
kubectl get svc
kubectl describe svc mongodb-service           # Confirm Endpoints point to the pod IP
```

**Output:**
```
deployment.apps/mongodb-deployment unchanged
service/mongodb-service created

NAME              TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)     AGE
kubernetes        ClusterIP   10.96.0.1     <none>        443/TCP     177m
mongodb-service   ClusterIP   10.98.113.4   <none>        27017/TCP   0s
```

**Describe (key fields):**
```
Selector:    app=mongodb
Type:        ClusterIP
IP:          10.98.113.4
Port:        27017/TCP
TargetPort:  27017/TCP
Endpoints:   10.244.0.8:27017
```

`Endpoints` matches the MongoDB pod IP — selector correctly bound the Service to the pod.

**Confirm pod IP matches the Service endpoint:**
```bash
kubectl get pod -o wide
```

```
NAME                                 READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
mongodb-deployment-df5cd6568-tb9dl   1/1     Running   0          12m   10.244.0.8   minikube   <none>           <none>
```

Pod IP `10.244.0.8` = Service `Endpoints` value above.

**All MongoDB resources at a glance:**
```bash
kubectl get all | grep mongodb
```

```
pod/mongodb-deployment-df5cd6568-tb9dl   1/1     Running   0          13m
service/mongodb-service                  ClusterIP   10.98.113.4   <none>   27017/TCP   3m23s
deployment.apps/mongodb-deployment       1/1     1            1            13m
replicaset.apps/mongodb-deployment-df5cd6568   1   1           1            13m
```

Pod + Service + Deployment + ReplicaSet — all four resource types tied to the `mongodb` app label.

### Step 4: MongoDB ConfigMap (`mongo-configmap.yaml`)

Stores the MongoDB service hostname so Mongo Express can resolve it. **Non-sensitive** config (no encryption needed) — perfect ConfigMap use case.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongodb-configmap
data:
  database_url: "mongodb-service:27017"
```

**Key points:**
- `database_url` matches the internal Service name from Step 3
- Mongo Express resolves `mongodb-service` via cluster DNS to `10.98.113.4`
- ConfigMap must exist **before** the Mongo Express Deployment that references it

```bash
kubectl apply -f K8S-Config-Files/mongo-configmap.yaml
kubectl get configmap
kubectl describe configmap mongodb-configmap
```

### Step 5: Mongo Express Deployment (`mongo-express.yaml`)

Web UI for MongoDB. Pulls **credentials from the Secret** and **MongoDB hostname from the ConfigMap**.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express
  labels:
    app: mongo-express
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
      - name: mongo-express
        image: mongo-express
        ports:
        - containerPort: 8081
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
        - name: DATABASE_URL
          valueFrom:
            configMapKeyRef:
              name: mongodb-configmap
              key: database_url
        - name: ME_CONFIG_MONGODB_URL
          value: "mongodb://$(ME_CONFIG_MONGODB_ADMINUSERNAME):$(ME_CONFIG_MONGODB_ADMINPASSWORD)@$(DATABASE_URL)"
```

**Key points:**
- `containerPort: 8081` — Mongo Express web UI port
- Two env vars from **Secret** (creds), one from **ConfigMap** (host)
- `ME_CONFIG_MONGODB_URL` uses K8s `$(VAR)` substitution to build the connection URI from the other env vars
- Final URI: `mongodb://username:password@mongodb-service:27017`

**Prerequisites order:** Secret → ConfigMap → Deployment

```bash
kubectl apply -f K8S-Config-Files/mongo-express.yaml
kubectl get pod
kubectl logs <mongo-express-pod>          # Check it connected to MongoDB
```

**Output:**
```
configmap/mongodb-configmap created
deployment.apps/mongo-express created

NAME                                 READY   STATUS    RESTARTS   AGE
mongo-express-5747d566b9-5z7vn       1/1     Running   0          24s
mongodb-deployment-df5cd6568-tb9dl   1/1     Running   0          24m
```

**Mongo Express logs (success):**
```
Waiting for mongodb-service:27017...
No custom config.js found, loading config.default.js
Welcome to mongo-express 1.0.2
------------------------
Mongo Express server listening at http://0.0.0.0:8081
Server is open to allow connections from anyone (0.0.0.0)
basicAuth credentials are "admin:pass", it is recommended you change this in your config.js!
```

`Waiting for mongodb-service:27017` confirms DNS resolution via the ConfigMap worked — Mongo Express found MongoDB through the internal Service.

### Step 6: Mongo Express External Service (appended to `mongo-express.yaml`)

A **LoadBalancer** Service exposes the Mongo Express UI outside the cluster. Appended to `mongo-express.yaml` as a second YAML document.

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 8081          # Service port (cluster-internal)
    targetPort: 8081    # Container port
    nodePort: 30000     # External node port (browser entry point)
```

**Key points:**
- `type: LoadBalancer` — in cloud envs provisions an external LB; on Minikube acts like NodePort + needs `minikube service` to expose
- `nodePort: 30000` — fixed external port (must be in range 30000–32767)
- **Note:** Module 22 best practice says no NodePort for external access in production — use Ingress/LoadBalancer. This demo uses NodePort for local Minikube simplicity.

**Apply & access:**
```bash
kubectl apply -f K8S-Config-Files/mongo-express.yaml
kubectl get svc
```

**Output:**
```
deployment.apps/mongo-express unchanged
service/mongo-express-service created

NAME                    TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
mongo-express-service   LoadBalancer   10.106.14.93   <pending>     8081:30000/TCP   0s
mongodb-service         ClusterIP      10.98.113.4    <none>        27017/TCP        19m
```

`EXTERNAL-IP: <pending>` is expected on Minikube — no real cloud LoadBalancer provisioner. Two ways to reach the UI:

**Option A — `minikube service` (preferred when minikube CLI works):**
```bash
minikube service mongo-express-service
```
Opens a tunnel + browser tab automatically.

**Option B — `kubectl port-forward` (fallback when minikube CLI is blocked):**
```bash
kubectl port-forward service/mongo-express-service 8081:8081
```
```
Forwarding from 127.0.0.1:8081 -> 8081
Forwarding from [::1]:8081 -> 8081
```
Then browse to **http://localhost:8081** — login `admin` / `pass`.

**Gotcha (this environment):** Minikube was started with `sudo` (docker driver, WSL2). `sudo minikube service ...` fails with `Profile "minikube" not found` because the profile lives under the user's home, not root's. Use `kubectl port-forward` instead — it works as the regular user since `kubectl` already talks to the cluster.

**Mongo Express UI — confirmed working:**

![Mongo Express UI accessed via port-forward](./Mongoexpress_external.png)

End-to-end flow verified: browser → `localhost:8081` (port-forward) → Service `mongo-express-service` → Pod `mongo-express` → reads creds from Secret + host from ConfigMap → connects to Service `mongodb-service:27017` → Pod `mongodb-deployment`.

### Summary
Built and ran a complete 2-tier app entirely with K8s primitives, in six discrete steps:
1. **Secret** (`mongodb-secret`) — base64 creds, mounted into pods via `secretKeyRef`
2. **MongoDB Deployment** (`mongodb-deployment`) — single replica, env vars from the Secret
3. **Internal Service** (`mongodb-service`, ClusterIP) — stable in-cluster DNS for MongoDB
4. **ConfigMap** (`mongodb-configmap`) — non-sensitive `database_url` for Mongo Express
5. **Mongo Express Deployment** (`mongo-express`) — pulls creds from Secret + host from ConfigMap, builds the connection URI via `$(VAR)` substitution
6. **External Service** (`mongo-express-service`, LoadBalancer + NodePort 30000) — exposes the UI; accessed locally via `kubectl port-forward 8081:8081`

### Conclusion
This module wires together **every major K8s primitive** in one working flow: Secret + ConfigMap for config-as-code separation (sensitive vs non-sensitive), Deployment for the workloads, internal ClusterIP for service-to-service communication, and external LoadBalancer/NodePort for ingress. The pattern — *Secret → Workload → internal Service → ConfigMap → consumer Workload → external Service* — generalizes to almost every stateful app + UI deployment you'll do in K8s. The remaining modules (8–24) introduce one new primitive at a time (Namespaces, Ingress, Volumes, StatefulSet, Helm, RBAC) on top of this same scaffold.

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

---

## Conclusion

This repo turns 24 modules of theory and demos into a single, searchable artifact: every command actually run, every manifest actually applied, and every gotcha actually hit (including environment-specific ones like the WSL2 + `sudo minikube` profile mismatch in Module 7).

**Themes that recur across the modules:**
- **Declarative over imperative** — YAML in Git beats ad-hoc `kubectl create` for anything that lives past a demo
- **Labels + selectors** are how every K8s object finds every other one — Service → Pod, Deployment → ReplicaSet, NetworkPolicy → workload
- **Config separation** — Secrets for sensitive data, ConfigMaps for everything else, never bake either into images
- **Production best practices (Module 22)** apply retroactively to every manifest written here: pin image tags, set probes, set resource requests/limits, avoid NodePort for production ingress, run ≥2 replicas

**By Module 24 you should be able to:** stand up a microservices app from scratch, package it as a reusable Helm chart, deploy multiple charts together with Helmfile, secure it with RBAC, expose it with Ingress, persist its state with Volumes, and operate it day-to-day with kubectl and a managed K8s service (EKS/GKE/AKS).

Until then, the [Progress Tracker](#progress-tracker) at the top of this README is the source of truth for what's done.
| Kubectx | https://github.com/ahmetb/kubectx#installation |