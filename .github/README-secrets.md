# GitHub Secrets — DigitalOcean Kubeconfig

This project's CI/CD workflow (`.github/workflows/deploy.yml`) authenticates to the
DigitalOcean Managed Kubernetes (DOKS) cluster using a kubeconfig stored as a
GitHub Actions secret named **`KUBE_CONFIG`**.

The kubeconfig is **never committed to the repo**. It only lives in two places:
1. Your local download (delete it after uploading, or keep it in an encrypted vault).
2. The GitHub repo's Actions secret store (encrypted at rest, only exposed at runtime).

---

## Steps

### 1. Download the kubeconfig from DigitalOcean
- Log in to https://cloud.digitalocean.com/
- Navigate to **Kubernetes** → select the cluster (`k8s-helm-demo`)
- Click the **Actions** menu (or **Overview** tab) → **Download Config File**
- This downloads a file like `k8s-helm-demo-kubeconfig.yaml`

### 2. Copy the file contents
- Open the downloaded `.yaml` file in a text editor
- Select all (Ctrl+A) and copy (Ctrl+C) — you'll paste the entire YAML in step 6

### 3. Open the repo's secret settings
- Go to the GitHub repo
- Click **Settings** (top nav, repo-level — not your user settings)
- In the left sidebar: **Secrets and variables** → **Actions**

### 4. Create a new secret
- Click **New repository secret**

### 5. Name the secret
- **Name:** `KUBE_CONFIG`
- (Must match exactly — the workflow references `secrets.KUBE_CONFIG`)

### 6. Paste the kubeconfig
- **Secret:** paste the full contents of the kubeconfig YAML
- Click **Add secret**

### 7. Verify
- The secret should appear in the list with a green checkmark
- GitHub will mask the value — you can't read it back, only overwrite or delete it

---

## Security Warning

- **NEVER** commit the kubeconfig file to the repo, even if gitignored. The
  `.gitignore` at the repo root excludes common kubeconfig patterns as a
  defensive measure, but the right place for the token is **GitHub Secrets only**.
- If you ever accidentally commit it: rotate the cluster credentials immediately
  via DigitalOcean (Cluster → Actions → **Reset Cluster Credentials**), then
  update the `KUBE_CONFIG` secret with the new file.
- Anyone with write access to the repo can read the secret at workflow runtime
  via `${{ secrets.KUBE_CONFIG }}`. Limit who has push access accordingly.
