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

---

## Merge Strategy (best practice)

When merging a feature PR into `k8s` (and later when merging `k8s` into `main`),
use **squash merge** unless there is a specific reason not to.

```bash
# Via GitHub CLI
gh pr merge <PR#> --squash --delete-branch

# Via the GitHub UI: green "Merge pull request" button → "Squash and merge"
```

### Why squash

| Axis | Rationale |
|------|-----------|
| **Fast** | One operation, one commit on the target branch. Reviewers answer "what changed when Module N landed?" by reading one commit instead of 16. |
| **Safe** | Atomic revert — `git revert <one-sha>` undoes the entire module. No SHA rewriting on the integration branch (unlike rebase). The full per-commit history is preserved **forever inside the closed PR** — GitHub never garbage-collects it, so nothing is lost. |
| **Industry default** | Squash is the default for GitHub Flow, trunk-based development, and most modern team policies. It keeps long-lived branches readable as a high-level changelog rather than a wall of WIP commits. |
| **Bootcamp-specific** | The iterative narrative for each module belongs in `README.md` and `PROGRESS.md` (where it's documented richly), not in `git log`. `k8s` and `main` then read cleanly as "list of completed modules". |

### When NOT to squash

- **Multiple logically-independent changes in one PR** that should each be revertable separately → use **merge commit** so each commit remains addressable.
- **A long-running shared branch that other developers have local copies of** → use **merge commit** (rebase would invalidate their local history).

For single-module feature branches in this repo, squash is always the right call.

### Squash commit message format

When squashing, GitHub auto-populates the commit message with all the squashed
commit subjects. **Replace it** with a clean summary that matches the PR title:

```
Module 16 complete: Helm Demo on DigitalOcean Managed K8s (#2)

<short paragraph summarizing what landed — same content as the PR body's
"What Was Built" section>
```

The PR number in parens (added automatically by GitHub) gives reviewers a
one-click link back to the full per-commit history.
