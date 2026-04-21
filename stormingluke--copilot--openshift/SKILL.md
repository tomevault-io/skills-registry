---
name: openshift
description: > Use when this capability is needed.
metadata:
  author: stormingluke
---

## OpenShift-Specific Patterns

### Security Context Constraints (SCCs)
- Default to `restricted-v2` SCC — covers most workloads
- Never use `privileged` SCC unless absolutely required and documented
- Create custom SCCs for specific needs rather than relaxing `anyuid`
- Use `oc adm policy add-scc-to-user` for service account binding
- Pod security admission: `restricted` profile by default

### Routes vs Ingress
- Prefer OpenShift Routes over Ingress for route features (TLS passthrough, edge, reencrypt)
- Use `route.openshift.io/v1` API
- Set TLS termination explicitly: `edge`, `passthrough`, or `reencrypt`
- Use wildcard DNS only in non-production

### Operator Lifecycle Manager (OLM)
- Package operators as OLM bundles with CSV (ClusterServiceVersion)
- Use `operator-sdk` for scaffolding and bundle generation
- CatalogSources: custom catalogs for internal operators
- InstallModes: `OwnNamespace`, `SingleNamespace`, `MultiNamespace`, `AllNamespaces`
- Maintain upgrade path via `replaces` or `skipRange` in CSV

### BuildConfigs and ImageStreams
- Prefer external CI (GitHub Actions, Tekton) over BuildConfigs for new projects
- If using ImageStreams, set scheduled import for base images
- Use `oc new-build` for quick prototyping only

### Networking
- Use OpenShift SDN or OVN-Kubernetes depending on cluster version
- NetworkPolicy is the standard — EgressNetworkPolicy for OpenShift-specific egress rules
- Service Mesh (Istio-based): use for mTLS, traffic management, observability

### Monitoring
- Use OpenShift built-in monitoring stack (Prometheus, Alertmanager)
- Create `ServiceMonitor` and `PodMonitor` resources for custom metrics
- PrometheusRule for alerting
- UserWorkload monitoring must be enabled by cluster admin

### Operator Development for OpenShift
- Ensure the operator runs under `restricted-v2` SCC
- Set `runAsNonRoot: true` in all pod specs
- Use UBI (Universal Base Image) for container images
- Include `disconnected` / air-gapped support (image mirroring)
- Test with `operator-sdk scorecard`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stormingluke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
