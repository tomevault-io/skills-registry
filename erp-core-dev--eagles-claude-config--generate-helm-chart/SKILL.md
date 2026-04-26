---
name: generate-helm-chart
description: Generate Helm chart for Kubernetes deployment Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# Generate Helm Chart

Generate a Kubernetes Helm chart with deployment, service, ingress, and HPA.

## What To Do

1. **Create chart structure**:
   ```
   deploy/helm/{service}/
     Chart.yaml
     values.yaml
     values.production.yaml
     templates/
       deployment.yaml
       service.yaml
       ingress.yaml    (if --with-ingress)
       hpa.yaml        (if --with-hpa)
       configmap.yaml
       secrets.yaml
   ```

2. **Configure values.yaml** with sensible defaults
3. **Add health checks** (liveness, readiness, startup probes)
4. **Add resource limits** (CPU, memory)

## Arguments
- `<service-name>`: Kubernetes service name
- `--with-ingress`: Add ingress template
- `--with-hpa`: Add horizontal pod autoscaler

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
