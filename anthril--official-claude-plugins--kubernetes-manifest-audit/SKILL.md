---
name: kubernetes-manifest-audit
description: Audit Kubernetes manifests, Helm charts, and Kustomize overlays against CIS Kubernetes Benchmark and NSA/CISA hardening — pod security, resources, probes, RBAC, networking, secrets, availability. Static, live, apply, runtime modes. Use when this capability is needed.
metadata:
  author: anthril
---

# Kubernetes Manifest Audit

ultrathink

<!-- anthril-output-directive -->
> **Output path directive (canonical — overrides in-body references).**
> All file outputs from this skill MUST be written under `.anthril/audits/kubernetes-manifest-audit/`.
> Run `mkdir -p .anthril/audits/kubernetes-manifest-audit` before the first `Write` call.
> Primary artefact: `.anthril/audits/kubernetes-manifest-audit/<artefact>`.
> Do NOT write to the project root or to bare filenames at cwd.
> Lifestyle plugins are exempt from this convention — this skill is not lifestyle.

## When to use

Run this skill when the user mentions:
- Kubernetes audit, k8s security
- CIS Kubernetes Benchmark
- Helm chart review, Kustomize review
- Pod security standards
- NSA/CISA Kubernetes Hardening Guide

Covers nine categories: pod security (`runAsNonRoot`, `readOnlyRootFilesystem`, `allowPrivilegeEscalation`, dropped capabilities, no host namespaces), resource requests and limits, liveness/readiness/startup probes, image hygiene (digest pinning, pull policy, scoped `imagePullSecrets`), secrets and config (no plaintext Secrets in Git, external secret operators), networking (NetworkPolicies, Service types, Ingress TLS), RBAC (per-workload ServiceAccounts, no wildcard verbs), availability (PodDisruptionBudgets, replicas, topology spread, anti-affinity), and Helm hygiene (values.schema.json, sensible defaults).

## Before You Start

1. **Determine operating mode.** `--live` reads from a real cluster via `kubectl`, runs `kube-bench` and `kube-hunter` if installed. `--apply` produces YAML patches or `kubectl patch` commands (cluster changes require an explicit second confirmation). `--runtime` runs a scoped chaos experiment against non-prod (chaos-mesh or a simple pod-kill) if configured.
2. **Enumerate manifest groups.** Run `scripts/list-manifests.sh`.
3. **Sub-agent budget.** One agent per chart / Kustomize overlay / manifest directory. Warn above 10.
4. **Load `.k8s-ignore`** for suppressions.
5. **Production-name guard.** In `--apply` or `--runtime`, refuse targets whose namespace or context contains `prod`/`production` without `--i-really-mean-prod`.

## User Context

$ARGUMENTS

Manifest inventory: !`bash "${CLAUDE_PLUGIN_ROOT}/skills/kubernetes-manifest-audit/scripts/list-manifests.sh"`

Live-mode tools: !`which kubectl 2>/dev/null || echo "kubectl:unavailable"` · !`which helm 2>/dev/null || echo "helm:unavailable"` · !`which kube-bench 2>/dev/null || echo "kube-bench:unavailable"`

---

## Audit Phases

### Phase 1: Discovery & Mode Selection

1. Parse inventory. Group manifests into audit units: one per Helm chart, one per Kustomize overlay, one per directory of raw manifests.
2. In `--live` mode, verify kubectl context is set and non-prod (or `--i-really-mean-prod` is present).
3. Confirm scope with the user; warn if >10 groups.

### Phase 2: Per-Group Snapshot

For each group, extract every manifest's `kind` and relevant fields:

- Deployments, StatefulSets, DaemonSets, Jobs, CronJobs — spec.template.spec (containers, securityContext, resources, probes, volumes), replicas, strategy
- Services, Ingresses — type, ports, TLS
- ConfigMaps, Secrets — data keys (never values), sealing status
- RBAC — ServiceAccounts, Roles/ClusterRoles, Bindings
- NetworkPolicies — selectors, ingress/egress rules
- PDBs, HPAs — target workloads and thresholds
- Helm-specific: `Chart.yaml`, `values.yaml`, `values.schema.json`, `templates/`

In `--live` mode, cross-reference with `kubectl get` output per namespace.

### Phase 3: Parallel Sub-Agent Audit

Spawn one `Agent(subagent_type=Explore)` per group (single assistant message). Each walks categories A–I from `reference.md` §1.

- **A. Pod security** — `runAsNonRoot`, `readOnlyRootFilesystem`, `allowPrivilegeEscalation: false`, dropped capabilities, no `hostNetwork`/`hostPID`/`hostIPC`, seccomp profile
- **B. Resources** — every container has `requests` + `limits` for cpu and memory; QoS tier appropriate
- **C. Probes** — liveness, readiness, startup configured; thresholds sensible
- **D. Image hygiene** — digest-pinned, `imagePullPolicy: IfNotPresent` (not `Always` in prod), `imagePullSecrets` scoped
- **E. Secrets & config** — no plaintext Secret YAML in Git (SealedSecrets / External Secrets / SOPS acceptable); ConfigMap not misused for secrets
- **F. Networking** — NetworkPolicy present for each workload namespace; Service type sensible; Ingress TLS
- **G. RBAC** — per-workload ServiceAccount; no wildcard `verbs: ["*"]` or `resources: ["*"]`
- **H. Availability** — PDB for critical workloads; `replicas > 1` for prod; topology spread or anti-affinity; rolling update surge/unavailable bounds
- **I. Helm hygiene** — `values.schema.json`, templated fields have defaults, no hardcoded production values in `values.yaml`

Sub-agents may read `kubectl get <kind> -o yaml` in `--live` mode but MUST NOT run `kubectl apply`, `kubectl delete`, `kubectl patch`, `helm install`, or `helm upgrade`.

### Phase 4: Merge & Risk Register

Merge sub-agent output. Cross-reference with kube-bench output if available (attach matching CIS IDs to findings). Assign `K8S-001…` IDs.

### Phase 5: Remediation Drafting

Emit commented YAML to `k8s-suggested.yaml`. Each block shows the target file:line, the evidence, and the fix.

For `--live` mode, alternatives as `kubectl patch` commands are included — but commented out, never executed.

### Phase 6: Apply Mode (opt-in)

Interactive `[a]pply / [s]kip / [A]ll / [q]uit` loop. YAML file edits go through `Edit`. `kubectl patch` execution requires the literal word `DESTROY` confirmation and writes both the patch command and the prior state to `apply-log.md`.

### Phase 7: Runtime Testing (opt-in)

When `--runtime` and a non-prod cluster is confirmed:
1. Identify the target Deployment (user-selected; defaults to the most-replicated non-system one).
2. Run a scoped chaos experiment: single pod deletion, confirm rolling recovery within its `progressDeadlineSeconds`.
3. Alternative: run `kubectl drain` on one node if `--chaos-node` flag is passed.
4. Record metrics from `kubectl top pods` pre/post if metrics-server is available.
5. Attach results to the report as "Runtime resilience test".

### Phase 8: Reporting

Write `kubernetes-manifest-audit.md` + `kubernetes-manifest-audit.json` + `k8s-suggested.yaml` (+ `cluster-state.json` in `--live` mode and `chaos-run.md` in `--runtime`).

---

## Scoring

Weights: A=20, B=15, C=10, D=10, E=15, F=10, G=10, H=5, I=5 (sum 100). See `reference.md` §3.

| Total | Verdict |
|---|---|
| 90+ | PASS |
| 70–89 | PASS WITH WARNINGS |
| 50–69 | CONDITIONAL |
| <50 | FAIL |

---

## Important Principles

- **Default security is insecure.** A Deployment with no `securityContext` runs as root with full capabilities. This is always at least HIGH.
- **No requests = best-effort QoS.** The first pod to be evicted under memory pressure. Flag every container missing requests.
- **Secrets in plaintext YAML belong outside Git.** SealedSecrets / External Secrets Operator / SOPS / cluster-managed Secrets are all acceptable alternatives.
- **Ingress without TLS is HIGH severity on prod.** Often downgraded to MEDIUM on internal-only ingress, but still flagged.
- **`replicas: 1` in prod is MEDIUM-HIGH.** A single pod is a single point of failure.
- **Helm's `values.yaml` is often production values.** Treat it as a manifest — it deploys real things.
- **Runtime chaos is non-prod only.** Never run a chaos experiment against a cluster whose context/namespace contains `prod`/`production` without `--i-really-mean-prod`.
- **Australian English. DD/MM/YYYY. Markdown-first.**

---

## Edge Cases

1. **Pure Helm chart (no rendered manifests in Git).** Run `helm template` to render, then audit the rendered output.
2. **Operator-managed CRDs.** Audit the CR spec; note that operator semantics may enforce additional rules outside this skill's view.
3. **GitOps repo (Argo CD / Flux).** Audit source manifests; in `--live` mode, note the sync state but do not edit.
4. **Cluster-scoped resources (ClusterRoles, ClusterRoleBindings).** Weight RBAC findings higher; cluster-wide scope amplifies blast radius.
5. **Mutating admission webhooks in cluster.** Rendered manifests may differ from deployed. In `--live` mode, cross-check.
6. **DaemonSets often need host namespaces.** CNI plugins, log shippers — flag but allow suppression.
7. **Jobs and CronJobs** — probes don't apply; resource requests still do.
8. **NetworkPolicy absence** — if the CNI doesn't enforce NetworkPolicy, skip F findings for that group (note in report).

---
> Source: [anthril/official-claude-plugins](https://github.com/anthril/official-claude-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-28 -->
