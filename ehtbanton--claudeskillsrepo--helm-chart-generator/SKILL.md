---
name: helm-chart-generator
description: Generate Helm chart files for Kubernetes application deployment. Triggers on "create helm chart", "generate helm template", "kubernetes helm", "helm package". Use when this capability is needed.
metadata:
  author: ehtbanton
---

# Helm Chart Generator

Generate Helm chart files for Kubernetes application deployment.

## Output Requirements

**File Output:** `Chart.yaml`, `values.yaml`, `templates/*.yaml`
**Format:** Valid Helm chart structure
**Standards:** Helm 3.x

## When Invoked

Immediately generate a complete Helm chart with templates and values.

## Example Invocations

**Prompt:** "Create Helm chart for web application"
**Output:** Complete Helm chart with deployment, service, and ingress templates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ehtbanton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
