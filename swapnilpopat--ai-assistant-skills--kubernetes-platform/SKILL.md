---
name: kubernetes-platform
description: Design and operate a managed Kubernetes platform (EKS, AKS, GKE) as an internal product — golden paths, multi-tenancy, autoscaling, supply-chain security, and progressive delivery. Use when standing up a new cluster strategy or maturing one beyond raw kubectl. Use when this capability is needed.
metadata:
  author: SwapnilPopat
---

# Kubernetes Platform

Treat the cluster like a product, not a toy. Workload teams should ship via a paved path (Helm/Kustomize + GitOps + policy) and never need to know which CNI you chose. Operators get day-2 covered: autoscaling, upgrades, security, multi-tenancy.

## Stack Baseline (2026)

| Concern | Recommended |
| --- | --- |
| Distribution | EKS 1.32, AKS 1.32, GKE 1.32 (or Autopilot/Fargate for low-ops) |
| Cluster IaC | Terraform + EKS Blueprints / AKS Bicep / GKE Terraform modules; Cluster API for fleets |
| GitOps | Argo CD 3.x or Flux 2.x; ApplicationSets for fleet rollouts |
| Packaging | Helm 3 + Kustomize overlays, or Timoni (CUE) for stricter typing |
| Service mesh (only if needed) | Istio Ambient, Linkerd 2.x, Cilium service mesh |
| Networking | Cilium CNI (eBPF), NetworkPolicy default-deny |
| Ingress | Gateway API 1.2 (HTTPRoute), or NGINX/Envoy/Contour |
| Autoscaling | Karpenter (AWS) / Cluster Autoscaler / GKE Autopilot; HPA + KEDA for events |
| Policy | Kyverno (preferred) or Gatekeeper/OPA; Pod Security Admission `restricted` |
| Supply chain | Sigstore (cosign) signing + verification, SLSA L3 build, SBOM (CycloneDX) |
| Runtime security | Falco, Tetragon, AWS GuardDuty for EKS |
| Observability | OpenTelemetry Operator, Prometheus + Mimir/Thanos, Loki, Tempo, Pixie |
| Progressive delivery | Argo Rollouts or Flagger (canary, blue-green, analysis) |
| Secrets | External Secrets Operator → Vault / KMS / Key Vault / Secret Manager |
| Cost | OpenCost, Karpenter consolidation, Spot/Preemptible for stateless |

## When to Use
- Multiple teams need isolated workloads on shared infra.
- Per-PR preview environments at scale.
- Need horizontal scaling beyond what PaaS offers.
- Standardizing on portable runtime across clouds.

## Don't Use When
- One small service, one team — a managed PaaS (Cloud Run, Container Apps, Render) is cheaper end-to-end.
- No platform team to own day-2.

## Prerequisites
- Cloud landing zone exists (`../landing-zones/SKILL.md`).
- IdP federation in place for cluster RBAC.
- Container registry with vulnerability scanning.
- CI/CD with OIDC to the cluster (no static kubeconfigs).

## Inputs the Agent Should Gather
- Cloud(s), region(s), HA target.
- Tenant model: namespace per team vs cluster per environment vs vCluster.
- Compliance scope (PCI / HIPAA / FedRAMP).
- Workload mix: stateless web, batch, GPU, stateful.
- Existing tooling investments (Argo vs Flux, Helm vs Kustomize).

## Instructions

### 1. Cluster topology
- ≥ 1 cluster per environment (prod, non-prod, sandbox); one cluster per region for prod HA.
- Three control-plane AZs; nodes in ≥ 2 AZs per nodegroup.
- Reserve a `system` nodegroup with taints for platform addons.
- Use Karpenter / Autopilot; avoid hand-tuned ASGs.

### 2. Multi-tenancy via namespaces + policy
```yaml
# Kyverno: enforce required labels and resource limits per namespace
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata: { name: tenant-baseline }
spec:
  validationFailureAction: Enforce
  rules:
    - name: require-owner-and-cost-center
      match: { any: [{ resources: { kinds: [Namespace] } }] }
      validate:
        message: "Namespace must have owner and cost-center labels"
        pattern:
          metadata:
            labels:
              owner: "?*"
              cost-center: "?*"
    - name: require-resource-limits
      match: { any: [{ resources: { kinds: [Pod] } }] }
      validate:
        message: "All containers need CPU and memory limits"
        pattern:
          spec:
            containers:
              - resources:
                  limits: { cpu: "?*", memory: "?*" }
                  requests: { cpu: "?*", memory: "?*" }
```
Combine with `ResourceQuota`, `LimitRange`, and `NetworkPolicy` default-deny per namespace.

### 3. GitOps as the only deploy path
```yaml
# Argo CD ApplicationSet: one app per env per service via PR labels
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata: { name: payments }
spec:
  generators:
    - matrix:
        generators:
          - list:
              elements:
                - { env: dev,  cluster: dev-eu  }
                - { env: prod, cluster: prod-eu }
          - git:
              repoURL: https://github.com/org/payments
              revision: HEAD
              files: [{ path: "deploy/{{env}}/values.yaml" }]
  template:
    metadata: { name: "payments-{{env}}" }
    spec:
      project: payments
      source:
        repoURL: https://github.com/org/payments
        path: charts/payments
        helm: { valueFiles: ["../deploy/{{env}}/values.yaml"] }
      destination: { server: "{{cluster}}", namespace: payments }
      syncPolicy: { automated: { prune: true, selfHeal: true } }
```
No `kubectl apply` from laptops in any environment.

### 4. Supply-chain trust
- Build images with reproducible builders (Docker BuildKit, ko, Buildpacks).
- Sign with cosign (keyless via OIDC).
- Generate SBOM (`syft`, `cyclonedx`).
- Admission verifies signature + provenance via Kyverno `verifyImages` or Sigstore policy-controller.
- Fail closed on unsigned images in prod.

### 5. Progressive delivery
```yaml
# Argo Rollouts canary with analysis
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata: { name: api }
spec:
  strategy:
    canary:
      steps:
        - setWeight: 10
        - pause: { duration: 5m }
        - analysis:
            templates: [{ templateName: success-rate }]
            args: [{ name: service, value: api }]
        - setWeight: 50
        - pause: { duration: 10m }
```
Analysis template queries Prometheus burn rate; auto-rollback on failure.

### 6. Day-2 operations
- **Upgrades:** N-1 always supported; rehearse on non-prod monthly.
- **Backups:** Velero for cluster state; provider snapshots for PVs.
- **Drains:** PodDisruptionBudgets on every workload.
- **Observability:** OTel Collector DaemonSet; one tracing/metrics/logging backend.
- **Cost:** OpenCost dashboard per namespace; Karpenter consolidation.

### 7. Security baseline
- Pod Security Admission `restricted` cluster-wide.
- Default-deny NetworkPolicy in every namespace.
- IRSA / Workload Identity / Azure Workload Identity — no node IAM.
- External Secrets Operator; never `kubectl create secret` from laptops.
- Falco / Tetragon for runtime detection routed to SIEM.

## Common Pitfalls

| Pitfall | Why it bites | Fix |
| --- | --- | --- |
| One mega-cluster for all envs | Blast radius; upgrades terrifying | Cluster per env, fleet via GitOps |
| Service mesh by default | High ops cost, unclear value | Add only when mTLS / advanced traffic policy is required |
| Manual `kubectl` in prod | Drift, no audit | GitOps only; kubectl read-only |
| `latest` image tags | Non-reproducible deploys | Pin by digest; signature-verified |
| Default-allow NetworkPolicy | Lateral movement on compromise | Default-deny per namespace |
| Hand-rolled autoscaling | Slow, expensive | Karpenter / Autopilot |
| Secrets in Helm values | Leak via Git, Argo UI | External Secrets Operator |
| No upgrade cadence | Stuck on EOL versions | Quarterly minor upgrade rehearsed |

## Output Format
1. Cluster topology diagram (envs × regions × nodegroups).
2. Tenant model + namespace baseline (quotas, policies, network).
3. GitOps repo layout (app of apps / ApplicationSets).
4. Supply-chain pipeline: build → sign → SBOM → admit.
5. Progressive delivery template.
6. Day-2 runbook index (upgrade, backup, incident, drain).
7. Cost allocation model.

## Abort Criteria
- No platform team capacity for day-2 — recommend managed PaaS.
- Single workload only — Kubernetes overhead exceeds value.
- No landing zone / IdP — fix prerequisites first.

## Related Skills
- `../landing-zones/SKILL.md`
- `../finops/SKILL.md` — OpenCost, Karpenter consolidation
- `../../security/secure-development/SKILL.md` — supply chain
- `../../engineer/devops-practices/` — GitOps, progressive delivery
- `../../architect/quality-attributes/slo-error-budgets/SKILL.md`

## Authoritative References
- Kubernetes docs — Pod Security Admission, NetworkPolicy, Gateway API
- CNCF — Argo, Flux, Cilium, Kyverno, OpenTelemetry projects
- AWS EKS Best Practices Guide
- Azure AKS Baseline architecture
- Google GKE Enterprise / Autopilot docs
- Sigstore + SLSA framework
- CIS Kubernetes Benchmark

---
> Source: [SwapnilPopat/ai-assistant-skills](https://github.com/SwapnilPopat/ai-assistant-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
