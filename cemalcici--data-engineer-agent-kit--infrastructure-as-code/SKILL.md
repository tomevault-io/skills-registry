---
name: infrastructure-as-code
description: IaC patterns with Terraform, Pulumi, and Helm for data infrastructure. Use when this capability is needed.
metadata:
  author: cemalcici
---

# Infrastructure as Code

> **Learn to THINK in declarations, not manual clicks.**

## ⚠️ Core Principles

### Declarative
- Define desired state
- Let tool reconcile
- Idempotent operations

### Version Controlled
- Git for infrastructure
- Code review for changes
- Audit trail

---

## Common Patterns

### Terraform - Kubernetes Cluster
```hcl
# main.tf
resource "kubernetes_namespace" "data" {
  metadata {
    name = "data-platform"
    labels = {
      environment = var.environment
    }
  }
}

resource "helm_release" "spark_operator" {
  name       = "spark-operator"
  repository = "https://kubeflow.github.io/spark-operator"
  chart      = "spark-operator"
  namespace  = kubernetes_namespace.data.metadata[0].name

  set {
    name  = "webhook.enable"
    value = "true"
  }
}

resource "helm_release" "kafka" {
  name       = "kafka"
  repository = "https://charts.bitnami.com/bitnami"
  chart      = "kafka"
  namespace  = kubernetes_namespace.data.metadata[0].name

  values = [file("${path.module}/values/kafka.yaml")]
}
```

### Pulumi - Python
```python
import pulumi
import pulumi_kubernetes as k8s

namespace = k8s.core.v1.Namespace(
    "data-platform",
    metadata=k8s.meta.v1.ObjectMetaArgs(
        name="data-platform"
    )
)

spark_operator = k8s.helm.v3.Release(
    "spark-operator",
    chart="spark-operator",
    repository_opts=k8s.helm.v3.RepositoryOptsArgs(
        repo="https://kubeflow.github.io/spark-operator"
    ),
    namespace=namespace.metadata.name,
)
```

### Helm - Spark Application
```yaml
# values.yaml
spark:
  image: spark:3.5
  driver:
    cores: 1
    memory: "2g"
  executor:
    instances: 3
    cores: 2
    memory: "4g"

minio:
  endpoint: "http://minio:9000"
  accessKey: admin
  secretKey: password
```

### GitOps with ArgoCD
```yaml
# application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: data-platform
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/org/data-platform
    path: k8s/overlays/production
    targetRevision: HEAD
  destination:
    server: https://kubernetes.default.svc
    namespace: data-platform
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

---

## Best Practices

| Practice | Reason |
|----------|--------|
| State in remote backend | Team collaboration |
| Modules for reuse | DRY principle |
| Environment separation | Isolation |
| Secrets in vault | Security |

---

## Anti-Patterns

| Anti-Pattern | Solution |
|--------------|----------|
| Local state | Use remote backend |
| Hardcoded values | Use variables |
| No environment separation | Use workspaces/overlays |

---

## Related Skills

- For K8s workloads: `kubernetes-data`
- For Docker: `docker-data`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cemalcici) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
