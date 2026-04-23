---
name: gitops-patterns
description: name: gitops-patterns Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: gitops-patterns
description: GitOps patterns with ArgoCD and Flux
---

# GitOps Patterns

> Git as the source of truth for infrastructure.

---

## GitOps Flow

```
Code Change → PR → Merge → CI Build → Image Push → Git Sync → Deploy
```

| Step | Tool |
|------|------|
| CI Build | Azure DevOps |
| Image Push | Azure Container Registry |
| Git Sync | ArgoCD / Flux |
| Deploy | AKS |

---

## Repository Structure

```
gitops-repo/
├── base/                 # Base manifests
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
├── overlays/
│   ├── dev/
│   │   └── kustomization.yaml
│   ├── staging/
│   │   └── kustomization.yaml
│   └── prod/
│       └── kustomization.yaml
```

---

## ArgoCD Application

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/org/gitops-repo
    targetRevision: HEAD
    path: overlays/prod
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

---

## Flux Configuration

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: myapp
spec:
  interval: 1m
  url: https://github.com/org/gitops-repo
  ref:
    branch: main
---
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: myapp
spec:
  interval: 10m
  path: ./overlays/prod
  sourceRef:
    kind: GitRepository
    name: myapp
```

---

## DO / DON'T

| ✅ Do | ❌ Don't |
|-------|---------|
| Git for all changes | kubectl apply manually |
| Automated sync | Manual deployments |
| Environment overlays | Duplicate manifests |
| PR reviews | Direct pushes |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
