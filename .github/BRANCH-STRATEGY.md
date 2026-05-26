# Branch Strategy

This repo follows a GitFlow-style branch model so `main` stays stable and
production-ready while bootcamp modules iterate on feature branches.

---

## Branches

| Branch | Purpose | Direct pushes? |
|--------|---------|----------------|
| `main` | Stable, production-ready only. Reflects fully reviewed bootcamp progress. | **Never** — merged into only via PR from `k8s` |
| `k8s` | Integration branch for all bootcamp modules. Modules merge here when complete. | Allowed (but prefer PRs from feature branches) |
| `helm-demo-managed-k8s` | Feature branch for **Module 16 — Helm Demo: Stateful App on Kubernetes (DOKS)**. | Yes — primary working branch for this module |

---

## Merge Order

```
helm-demo-managed-k8s  ──►  k8s  ──►  main
   (feature branch)      (integration)   (stable)
```

1. **Work happens on `helm-demo-managed-k8s`** — every commit for Module 16 lands here.
   - Push triggers the GitHub Actions `Deploy to DOKS` workflow (`.github/workflows/deploy.yml`).
2. **When the module is complete**, merge `helm-demo-managed-k8s` → `k8s` (via PR).
   - This is when the Module 16 progress tracker box is flipped to `[x]`.
3. **When `k8s` is fully reviewed and stable**, merge `k8s` → `main` (via PR).
   - `main` is the public-facing, reference-quality version of the bootcamp repo.

---

## Rules

- ❌ Never `git push origin main` directly.
- ❌ Never force-push to `main` or `k8s`.
- ✅ All changes to `main` come via reviewed PRs from `k8s`.
- ✅ Each module gets its own feature branch (e.g. `helm-demo-managed-k8s`,
  `microservices-demo`, etc.); none of them push to `main` directly.
- ✅ Delete feature branches after they've been merged into `k8s`.
