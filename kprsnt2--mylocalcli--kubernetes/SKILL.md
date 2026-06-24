---
name: kubernetes
description: Kubernetes deployment best practices including resource management, security, and observability. Use when this capability is needed.
metadata:
  author: kprsnt2
---

# Kubernetes Best Practices

## Resource Management
- Always set resource requests/limits
- Use Horizontal Pod Autoscaler
- Set appropriate QoS classes
- Use PodDisruptionBudgets

## Deployments
- Use rolling updates
- Set proper readiness/liveness probes
- Use init containers for setup
- Configure proper termination grace period
- Use anti-affinity for HA

## ConfigMaps & Secrets
- Use ConfigMaps for config
- Use Secrets for sensitive data
- Mount as files, not env vars
- Use external secret managers
- Version configs with checksums

## Networking
- Use Services for internal traffic
- Use Ingress for external traffic
- Implement NetworkPolicies
- Use service mesh for complex routing

## Security
- Use RBAC
- Run as non-root
- Use PodSecurityPolicies/Standards
- Scan images in CI
- Use namespace isolation

## Observability
- Implement structured logging
- Export Prometheus metrics
- Use distributed tracing
- Set up alerting

---
> Source: [kprsnt2/MyLocalCLI](https://github.com/kprsnt2/MyLocalCLI) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
