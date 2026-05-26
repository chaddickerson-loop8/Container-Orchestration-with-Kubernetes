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

```bash
# Commands will be added as each step is completed
```

<!-- Steps: K8s cluster on DigitalOcean (DOKS), MongoDB StatefulSet via Helm, MongoExpress, NGINX Ingress -->

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