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
│   ├── mongo-express.yaml
│   ├── dashboard-ingress.yaml
│   ├── mosquitto-without-volumes.yaml
│   ├── config-file.yaml
│   ├── secret-file.yaml
│   └── mosquitto.yaml
├── Screenshots/           # Screenshots referenced from README modules (per-module subfolders)
│   ├── Module-07/
│   │   └── mongo-express-external.png
│   ├── Module-10/
│   │   ├── dashboard-first-access-1.png
│   │   ├── dashboard-first-access-2.png
│   │   ├── dashboard-first-access-3.png
│   │   └── dashboard-after-portforward.png
│   ├── Module-12/
│   └── Module-16/
└── Documents/             # Reference documents
```

---

## Progress Tracker
- [ ] Module 1: Introduction to Kubernetes
- [ ] Module 2: Basic Concepts & K8s Components
- [ ] Module 3: Kubernetes Architecture
- [x] Module 4: Minikube & kubectl — Local Setup
- [x] Module 5: kubectl CLI — Main Commands
- [x] Module 6: YAML Configuration Files
- [x] Module 7: Demo — Deploy MongoDB & Mongo Express
- [ ] Module 8: Namespaces
- [ ] Module 9: Kubernetes Services
- [x] Module 10: Ingress
- [ ] Module 11: Persisting Data with Volumes
- [x] Module 12: ConfigMap & Secret Volume Types
- [ ] Module 13: StatefulSet — Deploying Stateful Apps
- [ ] Module 14: Managed Kubernetes Services
- [x] Module 15: Helm — Package Manager
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

### Summary
Installed Minikube + kubectl and started a local single-node Kubernetes cluster using the Docker driver. Verified the cluster with `kubectl get nodes` (single control-plane node, `Ready`, v1.35.1).

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

### Conclusion
Local K8s sandbox is live. Every subsequent module (5–24) runs against this Minikube cluster — no cloud account needed. **Gotcha to remember:** because Minikube was started with `sudo` (Docker driver), the profile lives under root and the user's `minikube` CLI can't see it without sudo; `kubectl` works fine either way.

---

## Module 5: kubectl CLI — Main Commands
> *Core commands for managing K8s resources from the terminal.*

### Summary
Covered the core kubectl verbs for the full resource lifecycle: **inspect** (`get`, `describe`), **create** (imperative `create` vs declarative `apply -f`), **update** (`set image`, `edit`), **debug** (`logs`, `exec -it`), and **delete**. Saw rolling updates triggered by image changes and used `exec` to shell into a running pod.

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

### Conclusion
`kubectl` is the universal interface to any K8s cluster — local Minikube or production EKS/GKE/AKS. The declarative `apply -f` workflow is the production standard because YAML lives in Git (versioned, repeatable, code-reviewable). Imperative commands are best kept to learning and ad-hoc debugging. Module 6 builds directly on this by going YAML-only.

---

## Module 6: YAML Configuration Files
> *Declarative configuration — the preferred way to manage K8s resources.*

### Summary
Wrote paired manifests — `nginx-deployment.yaml` (2 replicas, label `app: nginx`) and `nginx-service.yaml` (selects `app: nginx`, routes service port `80` → container port `8080`). Applied both, confirmed Service `Endpoints` were auto-populated with both pod IPs, then cleaned up with `kubectl delete -f`.

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

### Conclusion
YAML manifests are the unit of Kubernetes configuration: declarative, source-controllable, and idempotent under `kubectl apply`. **Label/selector matching is the connective tissue** of K8s — it's how a Service finds its Pods, how a Deployment finds its ReplicaSet, and how a ReplicaSet finds its Pods. Module 7 stacks five different manifest kinds (Secret, ConfigMap, Deployment, Service ×2) into one working app and depends on this label-matching pattern throughout.

---

## Module 7: Demo — Deploy MongoDB & Mongo Express
> *Full demo: Secret → Deployment → Service (internal) → ConfigMap → Deployment → Service (external)*

### Summary
Built and ran a complete 2-tier app entirely with K8s primitives, in six discrete steps:
1. **Secret** (`mongodb-secret`) — base64 creds, mounted into pods via `secretKeyRef`
2. **MongoDB Deployment** (`mongodb-deployment`) — single replica, env vars from the Secret
3. **Internal Service** (`mongodb-service`, ClusterIP) — stable in-cluster DNS for MongoDB
4. **ConfigMap** (`mongodb-configmap`) — non-sensitive `database_url` for Mongo Express
5. **Mongo Express Deployment** (`mongo-express`) — pulls creds from Secret + host from ConfigMap, builds the connection URI via `$(VAR)` substitution
6. **External Service** (`mongo-express-service`, LoadBalancer + NodePort 30000) — exposes the UI; accessed locally via `kubectl port-forward 8081:8081`

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

![Mongo Express UI accessed via port-forward](./Screenshots/Module-07/mongo-express-external.png)

End-to-end flow verified: browser → `localhost:8081` (port-forward) → Service `mongo-express-service` → Pod `mongo-express` → reads creds from Secret + host from ConfigMap → connects to Service `mongodb-service:27017` → Pod `mongodb-deployment`.

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
> *Routing external HTTP/S traffic into the cluster via an Ingress Controller.*

### Summary
Stood up the **NGINX Ingress Controller** on Minikube and routed `http://dashboard.com` to the built-in Kubernetes Dashboard via an Ingress rule. Six steps: deploy the dashboard (`minikube dashboard`) → enable the `ingress` addon (installs NGINX controller in `ingress-nginx` namespace) → write `dashboard-ingress.yaml` (host `dashboard.com` → Service `kubernetes-dashboard:80`) → map `dashboard.com → 127.0.0.1` in **both** WSL `/etc/hosts` **and** Windows `C:\Windows\System32\drivers\etc\hosts` → expose the controller on a local port → hit `http://dashboard.com:8080` in the browser.

**Important gotcha for this environment:** `minikube dashboard` alone does **not** validate the Ingress demo — it opens an ephemeral `kubectl proxy` tunnel directly to the dashboard Service, bypassing the Ingress entirely. To actually test the Ingress rule you need the NGINX controller to be reachable on a port the browser can hit (`minikube tunnel` OR `kubectl port-forward`). In this WSL2 + sudo-Docker setup, `minikube tunnel` requires interactive sudo, so `kubectl port-forward` on port `8080` was used instead — same routing path through NGINX → Ingress → Service, just on a non-privileged port.

### Step 1: Open the Kubernetes Dashboard
```bash
minikube dashboard --url
# Opens kubectl proxy + prints a URL; deploys kubernetes-dashboard Deployment + Service
```

**Output (key parts):**
```
* Enabling dashboard ...
  - Using image docker.io/kubernetesui/dashboard:v2.7.0
* Verifying dashboard health ...
* Launching proxy ...
http://127.0.0.1:41881/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```

The dashboard is now running in the `kubernetes-dashboard` namespace:
```bash
kubectl get all -n kubernetes-dashboard
```
```
NAME                                READY   STATUS    RESTARTS   AGE
pod/kubernetes-dashboard-...        1/1     Running   0          2m

NAME                                TYPE        CLUSTER-IP       PORT(S)
service/kubernetes-dashboard        ClusterIP   10.96.131.140    80/TCP
```

The Service `kubernetes-dashboard` (ClusterIP, port 80) is the Ingress backend target.

### Step 2: Enable the Ingress Addon (NGINX Controller)
```bash
minikube addons enable ingress
# Installs ingress-nginx controller (Deployment + Service) into ingress-nginx namespace
```

**Verify the controller pod:**
```bash
kubectl get pods -n ingress-nginx
```
```
NAME                                        READY   STATUS      AGE
ingress-nginx-admission-create-xxxxx        0/1     Completed   25s
ingress-nginx-admission-patch-xxxxx         0/1     Completed   25s
ingress-nginx-controller-xxxxxxxxxx-xxxxx   1/1     Running     25s
```

The controller is a Deployment of an NGINX-based reverse proxy that watches `Ingress` resources and configures itself dynamically.

### Step 3: Create the Ingress Rule

**`K8S-Config-Files/dashboard-ingress.yaml`:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dashboard-ingress
  namespace: kubernetes-dashboard
spec:
  ingressClassName: nginx
  rules:
  - host: dashboard.com
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: kubernetes-dashboard
              port:
                number: 80
```

**Key points:**
- `namespace: kubernetes-dashboard` — Ingress must live in the same namespace as the backend Service it references
- `ingressClassName: nginx` — picks the NGINX controller installed by the addon
- `host: dashboard.com` — NGINX routes requests with this `Host:` header to the backend
- `backend.service.name: kubernetes-dashboard` + `port: 80` — points at the dashboard Service from Step 1

```bash
kubectl apply -f K8S-Config-Files/dashboard-ingress.yaml
kubectl get ingress -n kubernetes-dashboard
```

**Output:**
```
NAME                CLASS   HOSTS           ADDRESS        PORTS   AGE
dashboard-ingress   nginx   dashboard.com   192.168.49.2   80      30s
```

`ADDRESS` = the Minikube node IP. On WSL2 + Docker driver this IP is **not** reachable from the Windows browser — see Step 5.

### Step 4: Map `dashboard.com` to localhost in Hosts Files

On WSL2 you must edit **two** hosts files — they're independent:

**WSL `/etc/hosts`** (used by `curl`, `ping` from inside WSL):
```bash
sudo sh -c 'echo "127.0.0.1 dashboard.com" >> /etc/hosts'
grep dashboard.com /etc/hosts
# 127.0.0.1 dashboard.com
```

**Windows `C:\Windows\System32\drivers\etc\hosts`** (used by the Windows browser):

Open **PowerShell as Administrator**, paste each line separately (single-line — avoids terminal wrapping breaking the command):
```powershell
$f="C:\Windows\System32\drivers\etc\hosts"
"127.0.0.1 dashboard.com" | Out-File $f -Encoding ASCII
```

**Verify from Windows PowerShell:**
```
ipconfig /flushdns
ping dashboard.com
# Pinging dashboard.com [127.0.0.1] with 32 bytes of data:
# Reply from 127.0.0.1: bytes=32 time<1ms TTL=128
```

**Encoding gotcha (burned ~30 min):** PowerShell's `>>` redirect writes **UTF-16 LE** by default. A UTF-16-encoded line inside an otherwise ASCII hosts file makes the Windows DNS resolver **silently skip** the entry and fall through to public DNS (which resolves `dashboard.com` to a real public IP — a Vercel app, in this case). Symptom: `ping dashboard.com` returns a public IP, browser hits the wrong site. Fix: rewrite the file with `Out-File -Encoding ASCII` so it's plain ASCII. Confirmed working when `ping dashboard.com` returns `127.0.0.1`.

### Step 5: Expose the NGINX Ingress Controller Locally

`Ingress.ADDRESS = 192.168.49.2` is on Docker's internal network — unreachable from the Windows browser. Two ways to bridge it:

**Option A — `minikube tunnel` (standard for Module 10):**
```bash
minikube tunnel
# Prompts for sudo (binds privileged ports 80/443) — keep the terminal open
```
After this, `http://dashboard.com` (port 80) works directly.

**Option B — `kubectl port-forward` (used here, no sudo needed):**
```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8080:80 --address 0.0.0.0
# Forwarding from 0.0.0.0:8080 -> 80
```
Then browse to `http://dashboard.com:8080`.

**Why this works:** the NGINX controller routes based on the `Host:` HTTP header, not the port. As long as the browser sends `Host: dashboard.com`, NGINX finds the matching Ingress rule and proxies the request to the `kubernetes-dashboard` Service.

**Why `minikube dashboard` alone doesn't demo the Ingress:** it spins up `kubectl proxy` on an ephemeral port and tunnels directly to the dashboard Service — short-circuiting NGINX entirely. The Ingress rule from Step 3 is never exercised. To prove Module 10, the browser must go **through** the NGINX Ingress Controller, which requires Option A or B above.

### Step 6: Verify in Browser

Browse to:
```
http://dashboard.com:8080
```

Path: browser → port-forward (8080→80) → `ingress-nginx-controller` pod → matches `Host: dashboard.com` → routes to `kubernetes-dashboard` Service (ClusterIP) → dashboard pod.

**First access — debugging the connection chain:**

![Dashboard first access — initial attempt](./Screenshots/Module-10/dashboard-first-access-1.png)
![Dashboard first access — Windows DNS still resolving to public IP](./Screenshots/Module-10/dashboard-first-access-2.png)
![Dashboard first access — connection refused before port-forward](./Screenshots/Module-10/dashboard-first-access-3.png)

**Working state — after `kubectl port-forward` on port 8080:**

![Kubernetes Dashboard reached via Ingress at dashboard.com:8080](./Screenshots/Module-10/dashboard-after-portforward.png)

### Key Learnings & Gotchas

- **Ingress = L7 router**, Service = L4 endpoint. The Ingress object is just *config* — the **Ingress Controller** (NGINX here) is the actual reverse-proxy pod that enforces it.
- **One controller per cluster** (typically). Multiple Ingress resources from many namespaces all share one NGINX deployment.
- **`namespace` must match the backend Service** — Ingress can only reference Services in its own namespace.
- **WSL2 has two hosts files** — WSL and Windows. The Windows browser only reads the Windows one.
- **PowerShell `>>` writes UTF-16** — corrupts the hosts file silently. Use `Out-File -Encoding ASCII`.
- **`minikube dashboard` bypasses Ingress** — don't use it to validate this module.
- **`minikube tunnel` needs sudo** — on this WSL2 + sudo-started Minikube, `kubectl port-forward` on a high port is a clean fallback that exercises the same path.

### Conclusion
Module 10 introduces the **first piece of K8s infrastructure that lives between the outside world and a Service**: the Ingress Controller. Up through Module 7, external access was either NodePort (Module 22 best-practice says no) or LoadBalancer (cloud-only / Minikube-pending). The Ingress pattern — **one external entry point + per-host/per-path rules + a shared NGINX controller** — is what every production cluster uses to fan out HTTPS traffic to dozens of microservices on a single IP and a single TLS certificate. The next modules (11–13) move down the stack to storage and stateful workloads, but every external HTTP/S route in those demos still flows through the controller wired up here.

---

## Module 11: Persisting Data with Volumes
> *PersistentVolume, PersistentVolumeClaim, StorageClass.*

<!-- Add notes and examples here -->

---

## Module 12: ConfigMap & Secret Volume Types
> *Mounting config files and secrets as volumes into pods.*

### Summary
Demonstrated the **other** way to consume ConfigMaps and Secrets in K8s — as **mounted volumes** rather than environment variables (the pattern used in Module 7). Built up the Mosquitto MQTT broker in seven steps: baseline Deployment (no volumes) → `exec` into the pod to inspect the default `/mosquitto/config/mosquitto.conf` → delete the baseline → create a `ConfigMap` carrying our own `mosquitto.conf` → create a `Secret` carrying a `secret.file` → re-deploy Mosquitto with `volumes:` + `volumeMounts:` pointing at both → verify the files appear inside the pod with correct content.

### Step 1: Baseline Mosquitto Deployment (no volumes)

Plain Mosquitto MQTT broker, no config and no secrets mounted yet — the starting point before adding ConfigMap and Secret **volumes** in later steps.

**`K8S-Config-Files/mosquitto-without-volumes.yaml`:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mosquitto
  labels:
    app: mosquitto
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mosquitto
  template:
    metadata:
      labels:
        app: mosquitto
    spec:
      containers:
        - name: mosquitto
          image: eclipse-mosquitto:2.0
          ports:
            - containerPort: 1883
```

```bash
kubectl apply -f K8S-Config-Files/mosquitto-without-volumes.yaml
kubectl get pods -l app=mosquitto
```

**Output:**
```
deployment.apps/mosquitto created
NAME                        READY   STATUS    RESTARTS   AGE
mosquitto-8bbb9c957-tp8cd   1/1     Running   0          22s
```

Pod runs the default `eclipse-mosquitto:2.0` image with no external config. Next steps will introduce a ConfigMap (mosquitto config file) and a Secret (credentials) mounted as **volumes** rather than env vars — the distinction this module teaches.

### Step 2: Inspect the Default Config Inside the Pod

Before mounting our own config, look at what ships in the image:

```bash
kubectl exec mosquitto-8bbb9c957-tp8cd -- cat /mosquitto/config/mosquitto.conf
```

Returns a ~40 KB reference config where **every actual setting is commented out**. The broker is running entirely on built-in defaults (anonymous access, listener on `1883`, no persistence). This is the file the ConfigMap volume will replace in Step 5.

### Step 3: Clean Up the Baseline Deployment

```bash
kubectl delete -f K8S-Config-Files/mosquitto-without-volumes.yaml
# deployment.apps "mosquitto" deleted from default namespace
```

We'll re-apply with the volume-mounted version after the ConfigMap and Secret exist.

### Step 4: Create the ConfigMap (`config-file.yaml`)

Holds the active `mosquitto.conf` contents as ConfigMap data. The key (`mosquitto.conf`) will become the **filename** when mounted as a volume.

**`K8S-Config-Files/config-file.yaml`:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: mosquitto-config-file
data:
    mosquitto.conf: |
        log_dest stdout
        log_type all
        log_timestamp true
        listener 9001
```

```bash
kubectl apply -f K8S-Config-Files/config-file.yaml
kubectl get configmap mosquitto-config-file
```

**Output:**
```
configmap/mosquitto-config-file created
NAME                    DATA   AGE
mosquitto-config-file   1      0s
```

`DATA: 1` = one key (`mosquitto.conf`). When mounted as a volume at `/mosquitto/config/`, this becomes a file at `/mosquitto/config/mosquitto.conf`.

### Step 5: Create the Secret (`secret-file.yaml`)

Holds a credential blob as base64-encoded data. Mounted the same way as the ConfigMap — the key (`secret.file`) becomes a filename inside the pod.

**`K8S-Config-Files/secret-file.yaml`:**
```yaml
apiVersion: v1
kind: Secret
metadata:
    name: mosquitto-secret-file
type: Opaque
data:
    secret.file: |
        VGVjaFdvcmxkMjAyMyEgLW4K
```

```bash
kubectl apply -f K8S-Config-Files/secret-file.yaml
kubectl get secret mosquitto-secret-file
```

**Output:**
```
secret/mosquitto-secret-file created
NAME                    TYPE     DATA   AGE
mosquitto-secret-file   Opaque   1      0s
```

`Opaque` = generic user-supplied secret (vs. typed secrets like `kubernetes.io/tls`). `DATA: 1` = one key (`secret.file`).

### Step 6: Re-deploy Mosquitto with Volume Mounts (`mosquitto.yaml`)

Same baseline Deployment as Step 1, **plus** `volumes:` at the pod spec level and `volumeMounts:` inside the container.

**`K8S-Config-Files/mosquitto.yaml`:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mosquitto
  labels:
    app: mosquitto
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mosquitto
  template:
    metadata:
      labels:
        app: mosquitto
    spec:
        containers:
          - name: mosquitto
            image: eclipse-mosquitto:2.0
            ports:
              - containerPort: 1883
            volumeMounts:
              - name: mosquitto-config
                mountPath: /mosquitto/config
              - name: mosquitto-secret
                mountPath: /mosquitto/secret
                readOnly: true
        volumes:
          - name: mosquitto-config
            configMap:
              name: mosquitto-config-file
          - name: mosquitto-secret
            secret:
              secretName: mosquitto-secret-file
```

**Key points:**
- `volumes:` declares two named volumes — one backed by the ConfigMap, one by the Secret
- `volumeMounts:` attaches each volume to a directory inside the container
- Each ConfigMap/Secret **key** (`mosquitto.conf`, `secret.file`) becomes a **file** under its mount path
- `readOnly: true` on the Secret mount is a sensible default — the pod has no reason to write back to it

```bash
kubectl apply -f K8S-Config-Files/mosquitto.yaml
kubectl get pods -l app=mosquitto
```

**Output:**
```
deployment.apps/mosquitto created
NAME                        READY   STATUS    RESTARTS   AGE
mosquitto-cf9f594cd-wn6gq   1/1     Running   0          38s
```

### Step 7: Verify the Volume Mounts

One command that captures everything — pod status, mount contents, and decoded files:

```bash
POD=$(kubectl get pod -l app=mosquitto -o jsonpath='{.items[0].metadata.name}') && \
  echo "=== Pod: $POD ===" && \
  kubectl get pod $POD && \
  echo "--- ls /mosquitto/config ---" && \
  kubectl exec $POD -- ls -la /mosquitto/config && \
  echo "--- ls /mosquitto/secret ---" && \
  kubectl exec $POD -- ls -la /mosquitto/secret && \
  echo "--- cat /mosquitto/config/mosquitto.conf ---" && \
  kubectl exec $POD -- cat /mosquitto/config/mosquitto.conf && \
  echo "--- cat /mosquitto/secret/secret.file ---" && \
  kubectl exec $POD -- cat /mosquitto/secret/secret.file
```

**Output (trimmed):**
```
=== Pod: mosquitto-cf9f594cd-wn6gq ===
mosquitto-cf9f594cd-wn6gq   1/1     Running   0   38s

--- ls /mosquitto/config ---
..data -> ..2026_05_22_21_17_13.1163115724
mosquitto.conf -> ..data/mosquitto.conf

--- ls /mosquitto/secret ---
..data -> ..2026_05_22_21_17_13.2049377479
secret.file -> ..data/secret.file

--- cat /mosquitto/config/mosquitto.conf ---
log_dest stdout
log_type all
log_timestamp true
listener 9001

--- cat /mosquitto/secret/secret.file ---
TechWorld2023! -n
```

**Two confirmations from this output:**
1. The ConfigMap's `mosquitto.conf` content (4 lines) replaced the 40 KB default — the volume mount **overlays** `/mosquitto/config/`.
2. The Secret's base64-encoded `VGVjaFdvcmxkMjAyMyEgLW4K` was decoded automatically by the Secret volume and written as plaintext `TechWorld2023! -n` to `/mosquitto/secret/secret.file`.

Same verification in one shot using `kubectl exec deploy/mosquitto -- sh -c '...'` against the Deployment directly (no need to look up the pod name):

```bash
kubectl exec deploy/mosquitto -- sh -c 'cat /mosquitto/config/mosquitto.conf; echo ---; cat /mosquitto/secret/secret.file'
```

![Mosquitto pod — ConfigMap and Secret volume mounts verified](./Screenshots/Module-12/mosquitto-volumes-verified.png)

**Atomic-update internals:** the `..data → ..<timestamp>` symlink pattern is how K8s does live ConfigMap/Secret updates — when you `kubectl apply` a change, the kubelet writes a new timestamped directory next to the old one, then atomically swaps the `..data` symlink. The container sees the new files appear in one consistent step, never half-updated.

### Conclusion
Module 12 closes the gap from Module 7: **the same** ConfigMap and Secret primitives can be consumed two completely different ways. **Env vars (Module 7)** are right for short scalar values like a hostname, username, or password that the app reads from its environment. **Volume mounts (Module 12)** are right for **files** — config files, TLS certs, signing keys, large blobs, anything the app expects to `open()` on disk. The volume pattern also gets you live updates for free (the `..data` symlink swap), so a ConfigMap edit propagates into running pods without a restart. Module 13 (StatefulSet) reuses both volume-style patterns alongside `PersistentVolumeClaim`s to give each replica its own stable storage.

<!-- Steps: Mosquitto deploy ✅, ConfigMap ✅, Secret ✅, Volume-mounted Deployment ✅, Verified ✅ -->

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
> *Deploy replicated MongoDB + MongoExpress + NGINX Ingress on a DigitalOcean managed Kubernetes cluster (DOKS).*

### Summary
Module 16 leaves Minikube behind and moves the demo onto a real managed Kubernetes cluster (DigitalOcean DOKS — `k8s-helm-demo`) driven entirely by GitHub Actions. The first milestone — covered in this update — is wiring the CI/CD pipeline end-to-end and proving the runner can authenticate against the cluster before any Helm work runs. A dedicated feature branch (`helm-demo-managed-k8s`) holds the workflow, the kubeconfig is stored as the `KUBE_CONFIG` repo secret (never on disk, never committed), and a "Verify cluster connection" step fails fast if the secret is wrong or the cluster is unreachable. With the connection proven, the pipeline now also installs Helm, registers the Bitnami repo, and verifies the MongoDB chart is resolvable — all before the real deploy lands.

**Status:** IN PROGRESS  
**Platform:** DigitalOcean Managed Kubernetes (DOKS)  
**Approach:** All operations run via GitHub Actions CI/CD pipeline

### CI/CD Pipeline Setup
- **Trigger:** push to `helm-demo-managed-k8s` branch (and `workflow_dispatch`)
- **Runner:** `ubuntu-latest`

### Pipeline Steps Completed
| Step | Description | Status |
|------|-------------|--------|
| Checkout code | Pull repo to runner | ✅ |
| Install kubectl | Set up kubectl on runner | ✅ |
| Configure kubeconfig | Write KUBE_CONFIG secret to `~/.kube/config` | ✅ |
| Verify cluster connection | `kubectl cluster-info` + `kubectl get nodes` | ✅ |
| Set up Helm | Install Helm on runner | ✅ |
| Verify Helm version | Confirm Helm installed correctly | ✅ |
| Add Bitnami repo | `helm repo add bitnami` | ✅ |
| Update Helm repos | `helm repo update` | ✅ |
| Verify MongoDB chart | `helm search repo bitnami/mongodb` | ✅ |
| Deploy MongoDB via Helm | `helm upgrade --install mongodb --values K8S-Config-Files/helm/helm-mongodb.yaml bitnami/mongodb` | ✅ |
| Wait for MongoDB rollout | `kubectl rollout status statefulset/mongodb --timeout=300s` | ✅ |
| Verify MongoDB pods | `kubectl get pod` | ✅ |
| Verify all resources | `kubectl get all` | ✅ |
| Verify MongoDB secrets | `kubectl get secret` | ✅ |
| Deploy Mongo Express | `kubectl apply -f K8S-Config-Files/helm/helm-mongo-express.yaml` | ✅ |
| Wait for Mongo Express rollout | `kubectl rollout status deployment/mongo-express --timeout=120s` | ✅ |
| Verify Mongo Express pod | `kubectl get pod` | ✅ |
| Check Mongo Express logs | `kubectl logs deployment/mongo-express` | ✅ |
| Add Ingress NGINX Helm repo | `helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx` | ✅ |
| Update Helm repos | `helm repo update` | ✅ |
| Install NGINX Ingress Controller | `helm upgrade --install nginx-ingress ingress-nginx/ingress-nginx --set controller.publishService.enabled=true` | ✅ |
| Wait for Ingress Controller rollout | `kubectl rollout status deployment/nginx-ingress-ingress-nginx-controller --timeout=180s` | ✅ |
| Wait for LoadBalancer IP | `kubectl wait --for=jsonpath='{.status.loadBalancer.ingress[0].ip}' svc/nginx-ingress-ingress-nginx-controller --timeout=300s` | ✅ |
| Verify pods | `kubectl get pod` | ✅ |
| Get services and LoadBalancer IP | `kubectl get svc` | ✅ |
| Apply Mongo Express Ingress rule | `kubectl apply -f K8S-Config-Files/helm/helm-ingress.yaml` | ✅ |
| Verify Ingress | `kubectl get ingress` | ✅ |
| Scale MongoDB down to zero | `kubectl scale --replicas=0 statefulset/mongodb` | ✅ |
| Verify pods after scale down | `kubectl get pod` | ✅ |
| Scale MongoDB back to 3 replicas | `kubectl scale --replicas=3 statefulset/mongodb` | ✅ |
| Wait for MongoDB rollout after scale up | `kubectl rollout status statefulset/mongodb --timeout=300s` | ✅ |
| Verify pods after scale up | `kubectl get pod` | ✅ |
| List Helm releases | `helm ls` | ✅ |
| Uninstall MongoDB Helm release | `helm uninstall mongodb` | ✅ |

### Next Steps
- Confirm external browser access to Mongo Express
- Capture screenshots of successful access
- Mark Module 16 complete

### Branch & Cluster Setup
- **Feature branch:** `helm-demo-managed-k8s` — per [`.github/BRANCH-STRATEGY.md`](./.github/BRANCH-STRATEGY.md), feature work lands here first, then promotes `feature → k8s → main`. `main` is never targeted directly by the CI pipeline.
- **Cluster:** `k8s-helm-demo` — 2-node pool on DigitalOcean, v1.36.0.
- **kubeconfig:** downloaded from the DigitalOcean console, pasted into the GitHub repo secret named **`KUBE_CONFIG`**. Full setup steps in [`.github/README-secrets.md`](./.github/README-secrets.md).

### CI/CD Workflow — [`.github/workflows/deploy.yml`](./.github/workflows/deploy.yml)
Two jobs, `build` → `deploy`. The `deploy` job authenticates against DOKS via the kubeconfig secret, then (eventually) runs Helm. All tooling is fetched directly from upstream — `kubernetes.io`, `helm.sh` — no third-party or cloud-vendor-specific actions.

**Trigger:**
```yaml
on:
  workflow_dispatch:
  push:
    branches:
      - helm-demo-managed-k8s
```

> **Gotcha — `workflow_dispatch` and non-default branches:** the "Run workflow" button in the Actions UI only surfaces for workflows that exist on the **default branch**. Since this workflow lives on `helm-demo-managed-k8s` and won't reach `main` until the module is complete, the `push:` trigger was enabled so every push to the feature branch auto-runs the pipeline.

**Authenticate against DOKS:**
```yaml
- name: Configure kubeconfig from GitHub Secret
  run: |
    mkdir -p $HOME/.kube
    echo "${{ secrets.KUBE_CONFIG }}" > $HOME/.kube/config
    chmod 600 $HOME/.kube/config
```

`chmod 600` is kept because the secret is decrypted to plain disk on the runner — `kubectl` warns on world/group-readable kubeconfigs, and the perms are a defense-in-depth bet against any future self-hosted runner.

### Verify Cluster Connection (Step 3b)
A new step sits between the kubeconfig write and the Helm install so a bad / expired secret fails fast with a clear error before any deploy work runs:

```yaml
- name: Verify cluster connection
  run: |
    kubectl cluster-info
    kubectl get nodes
```

**Output from the first successful run (`f57aae9` / run id `26474393325`):**
```
Kubernetes control plane is running at https://3d495365-...k8s.ondigitalocean.com
CoreDNS is running at https://3d495365-...k8s.ondigitalocean.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

NAME                        STATUS   ROLES    AGE   VERSION
pool-k8s-helm-demo-38e5s0   Ready    <none>   89m   v1.36.0
pool-k8s-helm-demo-38e5sd   Ready    <none>   89m   v1.36.0
```

![Successful Verify cluster connection step in GitHub Actions](./Screenshots/Module-16/CiCD-K8s_connection.png)

This confirms three things at once: the `KUBE_CONFIG` secret was written to the runner correctly, `kubectl` can authenticate against the DOKS API, and the cluster is reachable from GitHub-hosted runners.

### Step 3: Helm Setup and Bitnami Repository via CI/CD Pipeline
With the cluster connection proven, the pipeline now installs Helm directly on the runner and primes the Bitnami chart repository — the source of the MongoDB chart used in the next step. **All Helm operations run inside the GitHub Actions runner** — Helm is never installed on a developer machine, and no Helm state lives outside the workflow run (every run is ephemeral, so `helm repo add` + `helm repo update` are re-run each time).

**Final deploy job step order:**
1. **Checkout code** — pulls the repo so the workflow can reference local manifests or values files.
2. **Install kubectl** — fetches the latest stable `kubectl` from the official Kubernetes release server.
3. **Configure kubeconfig from GitHub Secret** — writes `${{ secrets.KUBE_CONFIG }}` to `$HOME/.kube/config` with `chmod 600`.
4. **Verify cluster connection** — `kubectl cluster-info` + `kubectl get nodes` to fail fast on a bad/expired kubeconfig before any Helm work runs.
5. **Set up Helm** — installs Helm 3 via the official `get-helm-3` script from `helm.sh`.
6. **Verify Helm version** — runs `helm version` to confirm the install succeeded.
7. **Add Bitnami Helm repository** — `helm repo add bitnami https://charts.bitnami.com/bitnami` registers the chart source on the runner.
8. **Update Helm repositories** — `helm repo update` pulls the latest chart index from Bitnami.
9. **Verify MongoDB chart available** — `helm search repo bitnami/mongodb` confirms the chart is resolvable before any deploy.
10. **Deploy placeholder** — `echo "MongoDB Helm deploy - coming next step"` — replaced with the real `helm upgrade --install` in the next step.

> **Note:** All Helm operations run inside the GitHub Actions runner. Each pipeline run is ephemeral — there is no persistent Helm state between runs, so the repo-add / repo-update / chart-search steps re-execute on every push.

**Pipeline success — Helm install + Bitnami repo + chart-search steps all green:**

![Helm Install Pipeline Success](./Screenshots/Module-16/Helm-iinstall-cicd-staus-sucess.png)

### Step 4: Helm Deployment of MongoDB with Replicas and Secrets
With Helm primed and the Bitnami MongoDB chart resolvable, the pipeline now performs the actual install — driven by a custom values file ([`K8S-Config-Files/helm/helm-mongodb.yaml`](./K8S-Config-Files/helm/helm-mongodb.yaml)). The values file is small on purpose: replica count + persistence + auth are the three knobs needed for a sane "production-shaped" MongoDB demo on DOKS. **All `helm install` / `kubectl get …` calls run inside the GitHub Actions runner — nothing is executed from a developer laptop.**

| Configuration | Value |
|---------------|-------|
| Architecture | `replicaset` |
| Replica Count | `3` |
| Storage Class | `do-block-storage` |
| Auth | `rootPassword` via Helm values |

**Pipeline steps 10–13 (added on top of the Step 3 sequence):**
10. **Deploy MongoDB via Helm** — `helm upgrade --install mongodb --values K8S-Config-Files/helm/helm-mongodb.yaml bitnami/mongodb` installs the chart on DOKS with the replica-set + persistence + auth overrides. `upgrade --install` is idempotent (installs if absent, upgrades if present) so re-running the pipeline doesn't error with "name already in use".
11. **Verify MongoDB pods** — `kubectl get pod` confirms the 3 replica-set pods (`mongodb-0`, `mongodb-1`, `mongodb-2`) reach `Running` status.
12. **Verify all resources** — `kubectl get all` shows the StatefulSet, headless Service, and any related Pod/PVC objects Helm rendered.
13. **Verify MongoDB secrets** — `kubectl get secret` confirms the chart created the `mongodb` Secret holding the root password (sourced from `K8S-Config-Files/helm/helm-mongodb.yaml`).

**Pipeline success — `helm install` + the three `kubectl get …` verify steps all green against DOKS:**

![Deploy MongoDB replica set on DOKS via Helm in CI/CD](./Screenshots/Module-16/deploy%20MongoDB%20replica%20set%20on%20DOKS%20via%20Helm%20in%20CICD.png)

**Reference:** [Bitnami MongoDB Helm Chart](https://github.com/bitnami/charts/tree/main/bitnami/mongodb)

> ⚠️ **Security note:** `rootPassword` in `K8S-Config-Files/helm/helm-mongodb.yaml` is for **demo purposes only**. In production, use GitHub Secrets (or a secrets manager like Vault / AWS Secrets Manager) and reference them via `--set` or sealed values. **Never commit real passwords to the repo.**

### Step 5: Web UI Setup for MongoDB Using Mongo Express and Secrets
With the MongoDB replica set live on DOKS, the pipeline now layers [Mongo Express](https://github.com/mongo-express/mongo-express) — a web-based MongoDB admin UI — on top, defined by [`K8S-Config-Files/helm/helm-mongo-express.yaml`](./K8S-Config-Files/helm/helm-mongo-express.yaml). Mongo Express authenticates against MongoDB as `root`, reading the password at runtime from the same `mongodb` Secret that the Bitnami chart created in Step 4 — so the credential never appears in the manifest, the image, or any committed file.

| Item | Value |
|------|-------|
| Service Type | `ClusterIP` (internal only) |
| Container Port | `8081` |
| Auth Method | Kubernetes Secret (`mongodb-root-password`) |
| MongoDB Server | `mongodb-0.mongodb-headless` |

**Pipeline steps 14–17 (added on top of the Step 4 sequence):**
14. **Deploy Mongo Express** — `kubectl apply -f K8S-Config-Files/helm/helm-mongo-express.yaml` creates the Deployment + ClusterIP Service in the `default` namespace.
15. **Wait for Mongo Express rollout** — `kubectl rollout status deployment/mongo-express --timeout=120s` blocks until the pod reaches Ready, so the next steps see a real running container.
16. **Verify Mongo Express pod** — `kubectl get pod` confirms `mongo-express-<hash>` is `1/1 Running` alongside the MongoDB replica-set pods.
17. **Check Mongo Express logs** — `kubectl logs deployment/mongo-express` surfaces the "Mongo Express server listening at http://0.0.0.0:8081" + DB-connect lines so a failed auth or DNS lookup shows up loud in CI.

**Pipeline success — Mongo Express applied, rolled out, and verified against the running MongoDB replica set (after the `helm upgrade --install` idempotency fix):**

![Mongo Express deploy + rollout + verify steps green after Helm upgrade --install fix](./Screenshots/Module-16/make%20Helm%20MongoDB%20deploy%20idempotent%20in%20CICD.png)

> **Note on the log output visible above:** the `mongo:27017` DNS-resolution failure is the official `mongo-express` image's entrypoint doing a TCP wait against a **hardcoded** `mongo` hostname (it doesn't honor `ME_CONFIG_MONGODB_SERVER` for the wait — only for the actual app config). The entrypoint moves on after the wait fails, and the app then connects via the env vars we set. Because the manifest currently has no readinessProbe, `kubectl rollout status` reports success the moment the container starts, not when the UI is actually serving — a Module 22 BP3 gap to close in a later iteration.

**Local access via port-forward (development/verification only):**
```bash
kubectl port-forward service/mongo-express-service 8081:8081
# Then open: http://localhost:8081
```

> ⚠️ **Security note:** Service is `ClusterIP` — only accessible inside the cluster. Port-forward is for development/verification only. External access will be configured via Ingress in the next step.

### Step 6: Exposing Mongo Express via Ingress NGINX on DigitalOcean
With the ClusterIP-only Mongo Express UI running, the last step is to make it reachable from the public internet. The pipeline installs the [NGINX Ingress Controller](https://github.com/kubernetes/ingress-nginx) via its official Helm chart, then applies an Ingress rule ([`K8S-Config-Files/helm/helm-ingress.yaml`](./K8S-Config-Files/helm/helm-ingress.yaml)) that routes `/` to `mongo-express-service:8081`. DigitalOcean automatically provisions a **LoadBalancer with a public IP** for the controller's Service — that IP becomes the public entrypoint to the cluster.

| Item | Value |
|------|-------|
| Ingress Controller | NGINX via Helm |
| Helm Chart | `ingress-nginx/ingress-nginx` |
| Service Type | `LoadBalancer` (DigitalOcean assigns public IP) |
| Ingress Path | `/` (Prefix) |
| Backend Service | `mongo-express-service:8081` |
| Access URL | `http://<LOADBALANCER-IP>` |

**Pipeline steps 18–25 (added on top of the Step 5 sequence):**
18. **Add Ingress NGINX Helm repo** — `helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx` registers the official upstream chart source.
19. **Update Helm repos** — `helm repo update` refreshes the chart index so the next step pulls the latest controller release.
20. **Install NGINX Ingress Controller** — `helm upgrade --install nginx-ingress ingress-nginx/ingress-nginx --set controller.publishService.enabled=true` installs (or upgrades) the controller. `upgrade --install` keeps the step idempotent across pushes — same idempotency pattern used for the MongoDB release in Step 10. `publishService.enabled=true` makes the controller publish the LoadBalancer's external address back into Ingress `status.loadBalancer` so downstream tooling can read the public IP.
21. **Wait for Ingress Controller rollout** — `kubectl rollout status deployment/nginx-ingress-ingress-nginx-controller --timeout=180s` blocks until the controller *pods* are Ready. Pods Ready ≠ cloud LoadBalancer provisioned, which is why the next step exists.
21b. **Wait for LoadBalancer IP** — `kubectl wait --for=jsonpath='{.status.loadBalancer.ingress[0].ip}' svc/nginx-ingress-ingress-nginx-controller --timeout=300s` blocks until DigitalOcean finishes provisioning the cloud LoadBalancer and populates `status.loadBalancer.ingress[0].ip`. Without this, the verify steps below race the LB provisioner and see `<pending>` (DO typically takes 1–3 minutes).
22. **Verify pods** — `kubectl get pod` confirms the controller pod is `Running` alongside the MongoDB + Mongo Express pods.
23. **Get services and LoadBalancer IP** — `kubectl get svc` surfaces the `nginx-ingress-ingress-nginx-controller` Service with its `EXTERNAL-IP` populated by DigitalOcean — that's the public address.
24. **Apply Mongo Express Ingress rule** — `kubectl apply -f K8S-Config-Files/helm/helm-ingress.yaml` registers the `/` → `mongo-express-service:8081` route with the controller.
25. **Verify Ingress** — `kubectl get ingress` confirms the rule is registered and surfaces the bound `ADDRESS` (the same LoadBalancer IP) for the Ingress object.
26. **Print Mongo Express access URL** — reads `status.loadBalancer.ingress[0].ip` back off the controller Service and echoes `Mongo Express is reachable at: http://<LB-IP>` so the public URL is visible directly in the CI log — no local `kubectl` required to find it.

> ⚠️ **Deprecation note:** `kubernetes.io/ingress.class` annotation is deprecated. Modern clusters prefer `spec.ingressClassName: nginx`. Safe for demo, but update before production use.

**Accessing Mongo Express externally:**
The pipeline's final step (`Print Mongo Express access URL`) echoes the public URL into the CI log — just open the linked workflow run and grab it from the log line:
```
Mongo Express is reachable at: http://<LB-IP>
```
Or query it locally if you have the DOKS kubeconfig:
```bash
kubectl get svc nginx-ingress-ingress-nginx-controller
# Look at the EXTERNAL-IP column — open http://<that-ip> in a browser.
```

**End-to-end proof — Mongo Express UI loaded in a browser via the public LoadBalancer IP:**

![Mongo Express UI reachable at http://134.209.140.28 — connected to mongodb-0, MongoDB 8.3.2, 23 active connections](./Screenshots/Module-16/wait%20for%20LoadBalancer%20IP%20and%20surface%20it%20in%20the%20CI%20log.png)

This is the full chain working: browser → DigitalOcean cloud LoadBalancer (`134.209.140.28`) → NGINX Ingress Controller pod → Ingress rule (`/` Prefix) → `mongo-express-service` ClusterIP → Mongo Express pod → MongoDB primary (`mongodb-0.mongodb-headless.default.svc.cluster.local:27017`) authenticated via the `mongodb-root-password` Secret. Server Status block (Hostname `mongodb-0`, 23 current connections) is live data pulled from the replica set in real time.

### Step 7: StatefulSet Scaling and Helm Release Verification
With the full stack proven reachable, the pipeline now exercises the StatefulSet's resilience: scale the MongoDB cluster all the way down to zero pods, confirm clean termination, scale it back up to three, wait for the replica set to reform, and finally list every Helm release that's making up the stack. This is the kind of operation an on-call engineer might run during a real incident — the pipeline scripts it so the behavior is deterministic and reproducible, not an ad-hoc fire-drill.

| Command | Purpose |
|---------|---------|
| `kubectl scale --replicas=0 statefulset/mongodb` | Simulate shutdown — test resilience and persistence |
| `kubectl get pod` | Verify pods terminated cleanly |
| `kubectl scale --replicas=3 statefulset/mongodb` | Restore MongoDB cluster to full operation |
| `kubectl rollout status statefulset/mongodb` | Wait for all 3 replicas to be Ready |
| `kubectl get pod` | Confirm all pods running after restore |
| `helm ls` | List all installed Helm releases |
| `helm uninstall mongodb` | Remove MongoDB and all associated K8s resources |

> **`helm uninstall` removes:**
> - MongoDB StatefulSet
> - MongoDB pods
> - MongoDB services
> - MongoDB secrets
> - MongoDB PersistentVolumeClaims
>
> Persistent volumes on DigitalOcean block storage may need manual deletion from the DO dashboard.

> **Why this works at all:** scaling a StatefulSet to zero and back confirms:
> - Persistent volumes retained data during shutdown
> - Replica set reforms correctly on scale up
> - DigitalOcean block storage survived the pod lifecycle

**Pipeline step summary (steps 26–32, appended after the Step 6 Ingress flow):**

| Step | Name | Status |
|------|------|--------|
| 26 | Scale MongoDB down to zero | ✅ |
| 27 | Verify pods after scale down | ✅ |
| 28 | Scale MongoDB back to 3 replicas | ✅ |
| 29 | Wait for MongoDB rollout after scale up | ✅ |
| 30 | Verify pods after scale up | ✅ |
| 31 | List Helm releases | ✅ |
| 32 | Uninstall MongoDB Helm release | ✅ |

**Persistence proof — data survived the StatefulSet scale-to-zero-and-back cycle:**

To make the persistence claim falsifiable, a `user-chad-example` database was **created via the Mongo Express UI *before* Step 7 ran** — i.e. *before* the pipeline scaled MongoDB down to 0 replicas. The screenshot below was taken *after* the pipeline finished scaling back up to 3 replicas:

![Mongo Express showing user-chad-example database still present after StatefulSet scale-to-zero-and-back — block storage retained data through pod restart](./Screenshots/Module-16/mongodb-persistence-proof.png)

`user-chad-example` is still in the database list. The PersistentVolumeClaims weren't deleted on scale-down — DigitalOcean Block Storage volumes stayed bound to their PVCs, and when the new pods came up they re-attached to the same volumes and replayed the data. This is the load-bearing reason a *StatefulSet* (not a Deployment) is the right primitive for MongoDB on Kubernetes.

### Conclusion
The Module 16 pipeline now stands up a complete MongoDB + Mongo Express stack on DOKS in a single push, *proves it survives a full operator-driven shutdown*, and tears MongoDB back down at the end as a teardown demo: cluster auth → Helm primed → MongoDB replica set (3 pods, DO block-storage volumes, root password from values) → Mongo Express UI wired up via secretKeyRef to the chart-generated Secret → NGINX Ingress Controller installed via its own Helm chart with a DigitalOcean LoadBalancer in front → public Ingress rule routing `/` to the UI → StatefulSet scaled to zero and back, replica set reformed cleanly off the persistent block-storage volumes, Helm releases enumerated, MongoDB release uninstalled. The "deploy → verify rollout → check logs → expose via Ingress → resilience demo → teardown" structure means any DNS, auth, image-pull, LoadBalancer provisioning, persistence/restart, **or uninstall** issue surfaces in CI rather than as a silent broken stack. With every phase green, Module 16 is complete — leading into Module 17's private-registry work.

## Module 16 Summary

### What We Learned
- Deploying an Ingress Controller via Helm on a cloud K8s provider
- Exposing internal ClusterIP services externally via Ingress rules
- Managing StatefulSet lifecycle through scaling operations
- Helm release management including install, verify, and uninstall
- DigitalOcean LoadBalancer integration with NGINX Ingress
- Securing MongoDB credentials using Kubernetes Secrets

### Full Pipeline Step Summary
| Step | Name |
|------|------|
| 1 | Checkout code |
| 2 | Install kubectl |
| 3 | Configure kubeconfig from GitHub Secret |
| 4 | Verify cluster connection |
| 5 | Set up Helm |
| 6 | Verify Helm version |
| 7 | Add Bitnami Helm repository |
| 8 | Update Helm repositories |
| 9 | Verify MongoDB chart available |
| 10 | Deploy MongoDB via Helm |
| 10b | Wait for MongoDB rollout |
| 11 | Verify MongoDB pods |
| 12 | Verify all resources |
| 13 | Verify MongoDB secrets |
| 14 | Deploy Mongo Express |
| 15 | Wait for Mongo Express rollout |
| 16 | Verify Mongo Express pod |
| 17 | Check Mongo Express logs |
| 18 | Add Ingress NGINX Helm repo |
| 19 | Update Helm repos |
| 20 | Install NGINX Ingress Controller |
| 21 | Wait for Ingress Controller rollout |
| 22 | Verify pods |
| 23 | Get services and LoadBalancer IP |
| 24 | Apply Mongo Express Ingress rule |
| 25 | Verify Ingress |
| 26 | Scale MongoDB down to zero |
| 27 | Verify pods after scale down |
| 28 | Scale MongoDB back to 3 replicas |
| 29 | Wait for MongoDB rollout after scale up |
| 30 | Verify pods after scale up |
| 31 | List Helm releases |
| 32 | Uninstall MongoDB Helm release |

### Files Created This Module
- `K8S-Config-Files/helm/helm-mongodb.yaml`
- `K8S-Config-Files/helm/helm-mongo-express.yaml`
- `K8S-Config-Files/helm/helm-ingress.yaml`
- `.github/workflows/deploy.yml`
- `.github/README-secrets.md`
- `.github/BRANCH-STRATEGY.md`

---

## Module 17: Deploy App from Private Docker Registry
> *Pull images from AWS ECR using K8s Secrets.*

### Summary
Module 17 switches the registry from public Docker Hub to a **private AWS Elastic Container Registry (ECR)** repository (`770535378489.dkr.ecr.us-east-1.amazonaws.com/my-app`). To pull from a private registry, Kubernetes needs credentials — a `kubernetes.io/dockerconfigjson` Secret built from a Docker auth config — and the Deployment has to reference that Secret via `imagePullSecrets`. Step 1 sets up the Docker side: authenticate locally against ECR with a 12-hour token, verify `~/.docker/config.json`, then carry the token into the Minikube VM so the in-VM Docker daemon can pull the same image.

### Step 1: Authenticate Docker and Minikube with AWS ECR

#### Prerequisite — clean cluster + working AWS creds
```bash
# Clean any leftover workloads from prior modules
kubectl delete deployment mongo-express mongodb-deployment mosquitto 2>/dev/null
kubectl delete service mongo-express-service mongodb-service mosquitto-service 2>/dev/null
kubectl delete configmap mongodb-configmap mosquitto-config 2>/dev/null
kubectl delete secret mongodb-secret mosquitto-secret-file 2>/dev/null
kubectl delete pvc --all 2>/dev/null
kubectl get all   # should only show `service/kubernetes`

# Confirm AWS CLI is authenticated against the right account
aws sts get-caller-identity
# Expected: Account = 770535378489 (matches the ECR registry)
```

#### Step 1 commands

| Command | Purpose |
|---------|---------|
| `kubectl get pods` | Verify clean environment — no pods running |
| `aws ecr get-login-password --region us-east-1 \| docker login --username AWS --password-stdin 770535378489.dkr.ecr.us-east-1.amazonaws.com` | Authenticate Docker with AWS ECR using temp token |
| `cat ~/.docker/config.json` | Verify Docker auth config saved correctly |
| `aws ecr get-login-password` | Generate ECR token separately |
| `aws ecr get-login-password --region us-east-1 > token.txt` | Save token to file without exposing in shell history |
| `minikube cp token.txt /home/docker/token.txt` | Copy token into Minikube VM |
| `minikube ssh` | Open shell inside Minikube VM |
| `cat ./token.txt` | Read saved token inside Minikube VM |
| `ls -a` | List all files including hidden |
| `minikube ssh -- 'cat /home/docker/token.txt \| docker login --username AWS --password-stdin 770535378489.dkr.ecr.us-east-1.amazonaws.com'` | **Run `docker login` *inside* the VM** so an inline `~/.docker/config.json` gets written there (required before Step 2 can copy it out) |
| `cat ~/.docker/config.json` | Verify Docker config inside Minikube VM (after in-VM login — should now show inline `auth: …`) |

**Private ECR Registry:**
```
770535378489.dkr.ecr.us-east-1.amazonaws.com/my-app
```

**The target repository in the AWS ECR console:**

![AWS ECR Console showing the my-app private repository in account 770535378489, region us-east-1, AES-256 encrypted, mutable tags](./Screenshots/Module-17/aws-ecr-private-repository-my-app.png)

Single repo (`my-app`) in the private registry. The full image path used everywhere downstream is `770535378489.dkr.ecr.us-east-1.amazonaws.com/my-app:<tag>` — that's what Step 3's Deployment will pull.

> ⚠️ **Security note:** ECR tokens expire every **12 hours**. Never commit `token.txt` or `config.json` to Git. Add `token.txt` to `.gitignore` immediately.

> 💡 **Docker Desktop on Windows + WSL gotcha:** after `docker login` succeeds on the host, `~/.docker/config.json` looks like:
> ```json
> {
>   "auths": { "770535378489.dkr.ecr.us-east-1.amazonaws.com": {} },
>   "credsStore": "desktop.exe"
> }
> ```
> The `auths` block is **empty** and the real token lives in the Windows credential store (`credsStore: "desktop.exe"`) — not inline in the file. A Kubernetes Secret built directly from this host file would carry no credentials and `kubectl create secret docker-registry --from-file=...` would silently produce a useless Secret. That's the load-bearing reason for the `token.txt → minikube cp → minikube ssh → docker login inside the VM` workflow above — it produces a real, inline `~/.docker/config.json` *inside the Minikube VM* that Step 2 can turn into a working Secret. On Linux hosts without Docker Desktop, the host `config.json` usually has the token inline and this dance isn't needed.

### Step 2: Create Kubernetes Secret for ECR Credentials
Once the in-VM `~/.docker/config.json` carries the inline ECR auth, the goal is to lift it back out and shape it into a Kubernetes Secret of type `kubernetes.io/dockerconfigjson`. That Secret is what the Deployment in Step 3 will reference via `imagePullSecrets` to authenticate the kubelet against ECR at pull time. A reference template is committed at [`K8S-Config-Files/my-registry-secret.yaml`](./K8S-Config-Files/my-registry-secret.yaml) — annotated for teaching, **never applied directly** (the real Secret is built from a live, inline `config.json`).

| Command | Purpose |
|---------|---------|
| `cd $HOME` | Navigate to home directory in PowerShell |
| `pwd` | Confirm current working directory |
| `minikube cp minikube:/home/docker/.docker/config.json .docker\config.json` | Copy Docker config from Minikube VM to local system |
| `cat ~/.docker/config.json` | View Docker auth credentials (sensitive — redacted) |
| `[Convert]::ToBase64String([IO.File]::ReadAllBytes("$HOME\.docker\config.json"))` | Base64 encode `config.json` for the Kubernetes Secret (PowerShell) |
| `base64 -w0 ~/.docker/config.json` | Same encoding from bash/WSL (bash equivalent of the PowerShell one-liner above) |

#### Method 1 — Create Secret from config file
```bash
kubectl create secret generic my-registry-key \
  --from-file=.dockerconfigjson="$HOME\.docker\config.json" \
  --type=kubernetes.io/dockerconfigjson
```

#### Method 2 — Create Secret using docker-registry type
```bash
kubectl create secret docker-registry my-registry-key-two \
  --docker-server=https://770535378489.dkr.ecr.us-east-1.amazonaws.com \
  --docker-username=AWS \
  --docker-password=<paste-ecr-token-here>
```

#### Verification
```bash
kubectl get secret
kubectl get secret -o yaml
```

> **Method 1 vs Method 2:**
> - **Method 1** reads directly from `config.json` — simpler, fewer flags.
> - **Method 2** passes credentials as explicit flags — more explicit, easier to script.
> - Both produce a `kubernetes.io/dockerconfigjson` Secret type.
> - **Use Method 2 in CI/CD pipelines** where the token comes from a GitHub Secret / vault — no on-disk `config.json` to manage.

> ⚠️ **Security note:** `kubectl get secret -o yaml` reveals the **base64-encoded** payload. Base64 is **not encryption** — anyone with read access to the cluster can decode it back to plaintext. For production, use **AWS Secrets Manager**, **Sealed Secrets**, or **External Secrets Operator** to keep raw credentials out of etcd.

#### Live run — what we actually observed
Executed end-to-end in this WSL shell against the local Minikube cluster:

```
$ kubectl create secret generic my-registry-key \
    --from-file=.dockerconfigjson=$HOME/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson
secret/my-registry-key created

$ TOKEN=$(aws ecr get-login-password --region us-east-1)
$ kubectl create secret docker-registry my-registry-key-two \
    --docker-server=https://770535378489.dkr.ecr.us-east-1.amazonaws.com \
    --docker-username=AWS \
    --docker-password="$TOKEN"
secret/my-registry-key-two created
$ unset TOKEN

$ kubectl get secret
NAME                  TYPE                             DATA   AGE
my-registry-key       kubernetes.io/dockerconfigjson   1      24s
my-registry-key-two   kubernetes.io/dockerconfigjson   1      13s
```

Both Secrets resolve to the same type (`kubernetes.io/dockerconfigjson`) and same key (`.dockerconfigjson`) — proving Method 1 and Method 2 produce structurally identical Secrets, just sourced differently. Either one can be referenced by the Deployment in Step 3 via `imagePullSecrets`.

**Live proof — both Secrets visible in the Minikube cluster:**

![Terminal showing kubectl get secret with my-registry-key (Method 1, 116s) and my-registry-key-two (Method 2, 105s), both kubernetes.io/dockerconfigjson, DATA=1](./Screenshots/Module-17/kubectl-get-secret-both-methods.png)

Both Secrets sit side-by-side with identical type and DATA count — the ~10s gap between AGEs matches the order we ran the two `kubectl create secret` commands.

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