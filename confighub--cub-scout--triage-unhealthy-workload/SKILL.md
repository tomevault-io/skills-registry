---
name: triage-unhealthy-workload
description: Use when the user is responding to a broken Kubernetes workload UNDER TIME PRESSURE — pager-going-off, CI-red, "prod is down" framing. Natural phrasing: "deploy/api is CrashLoopBackOff, what now?", "the rollout is stuck, help", "this pod won''t start", "pager fired on payments", "everything is red in argocd", "what should I check first?", "give me a triage plan for this workload". Composes doctor → explain → trace into a tight ~30-second loop the operator can run in a pager-acknowledged context. Do NOT load for: a planned investigation without urgency (use scout-diagnose directly), a drift question (use investigate-drift), or anything mutating (cub-scout never mutates — route to kubectl with the user driving).
metadata:
  author: confighub
---

# triage-unhealthy-workload

The pager-pressed loop. A workload is broken and the operator has 30 seconds before they need to make a decision. This skill composes `doctor` → `explain` → `trace` (plus targeted `kubectl` reads) into the tightest possible investigation path, with a checklist for what to look at FIRST.

## When to use

Explicit phrasings:

- "deploy/api is CrashLoopBackOff, what now?"
- "The rollout is stuck on `1/3 ready`, help"
- "This pod won't start — pager just fired"
- "Everything is red in Argo CD" / "Flux Kustomization is in `False` state"
- "What should I check first on this workload?"
- "Give me a triage plan for `<kind>/<name>`"
- "Production is degraded — what does cub-scout see?"

Implicit intents:

- Time pressure (`now`, `urgent`, `pager`, `prod is down`)
- The user wants a *plan*, not a full report — short, ordered, actionable
- The user already knows *what* is broken, they need to figure out *why*
- The user may need to escalate or roll back; they need evidence first

## Do not load for

- A planned investigation without urgency — [`scout-diagnose`](../scout-diagnose/SKILL.md) covers the verb group at leisure
- A drift question ("does live match git?") — [`investigate-drift`](../investigate-drift/SKILL.md)
- Pure inventory ("what's running here?") — [`scout-observe`](../scout-observe/SKILL.md)
- Incident postmortem evidence collection — [`operator-incident-evidence`](../operator-incident-evidence/SKILL.md)
- Anything mutating — cub-scout NEVER applies a fix. Route the user to `kubectl rollout undo`, `argocd app rollback`, or `cub` for ConfigHub mutations.

## The 30-second loop

```
1. doctor      → cluster-level signal (which namespace? which workloads?)
2. explain     → resource-level why (ownership + cause + nextSteps)
3. trace       → source chain (who delivered this? what revision?)
4. kubectl     → events + logs for the specific failure
5. decide      → wait / revert / escalate (operator drives mutations)
```

Each step is one cub-scout call. The full loop is typically 4–6 commands. If you need to go deeper, hand off to the verb-group skills.

## Step-by-step

### Step 1 — `doctor` (cluster signal)

```bash
$ cub-scout doctor --namespace prod --format json | jq '.summary, .issues[:5]'
```

`doctor` is the first-stop diagnostic. It returns:

- `summary` — count of healthy / degraded / failing workloads in scope
- `issues[]` — top-N issues ordered by severity, each with `kind/name`, primary cause, and a `nextSteps[]` array of read-only follow-up hints

If `doctor` already names the specific workload that broke, you're a step ahead.

### Step 2 — `explain` (resource why)

```bash
$ cub-scout explain deploy/api -n prod --presentation ai --format json
```

`explain` returns the plain-English answer for a single resource:

- `ownership` — which controller (if any) reconciles this; what labels signal it
- `cause` from the attribution layer — `controller-drift` / `manual-edit` / `unknown`
- `gitSource` / `bindingSource` — where the desired value comes from
- `events[]` — recent K8s events (added in `#368`; top 5, errors first)
- `nextSteps[]` — structured `actionType=read-only|waiting|human-decision` hints. Mutating actionTypes are filtered at emit-time.

The `--presentation ai` flag uses uppercase markers + bracket notation that LLMs reliably parse.

### Step 3 — `trace` (source chain)

```bash
$ cub-scout trace deploy/api -n prod --format json | jq '.chain, .secrets'
```

`trace` walks the ownership chain backwards:

- Argo CD: workload → Application → git revision (or OCI digest)
- Flux: workload → Kustomization/HelmRelease → source (GitRepository/OCIRepository/HelmRepository/Bucket) → revision
- ConfigHub: workload → ConfigHub Unit → Space → revision

It also surfaces **secret evidence** (`#328` / `#360`): which secrets the workload depends on, their resolution status (`present` / `missing` / `unreadable` / `unresolved`). A `missing` secret is often the silent killer.

### Step 4 — targeted `kubectl`

```bash
$ kubectl describe deploy api -n prod | grep -A 5 Conditions
$ kubectl get events --field-selector involvedObject.name=api -n prod --sort-by='.lastTimestamp'
$ kubectl logs deploy/api -n prod --tail 50 --previous   # crashlooping containers
```

Three targeted reads. The events list is the single most useful K8s native output for a triage; cub-scout's `explain` already inlines the top 5, but for deeper history go direct.

### Step 5 — decide

cub-scout returns evidence. The operator decides:

| Evidence | Likely action (operator-driven) |
|----------|-------------------------------|
| `cause: controller-drift`, `events: ImagePullBackOff` | Wait OR fix the image (kubectl set image, helm upgrade, or git commit) |
| `cause: manual-edit`, recent `kubectl-edit` writer | Revert via `kubectl rollout undo` OR commit the edit back to git |
| `cause: unknown`, no signal | Escalate; the cluster's `managedFields` is stripped or compromised |
| `secrets: missing/unresolved` | Check the secret + RBAC; the workload literally can't start |
| Argo `OutOfSync` + `controller-drift` | Argo will catch up; wait 30s and re-check |

None of these are cub-scout actions. cub-scout produces the evidence; the operator executes.

## Worked example

```bash
$ cub-scout doctor -n prod --format json | jq '.issues[0]'
{
  "kind": "Deployment", "name": "api", "namespace": "prod",
  "primaryCause": "ImagePullBackOff",
  "severity": "error",
  "nextSteps": [
    {"actionType": "read-only", "reason": "Image pull is failing; the registry may be down or the tag stale", "nextCommand": "cub-scout explain deploy/api -n prod"}
  ]
}

$ cub-scout explain deploy/api -n prod
Resource: Deployment/api in prod
  Owner:    Argo CD application "payments-api" (label:argocd.argoproj.io/instance)
  Status:   Degraded — 0/3 ready
  Events:   ImagePullBackOff (5 mins ago, repeating)
  Mutation cause: controller-drift (manager: argocd-controller)
  Git source: https://github.com/org/platform-config @abc123 path=apps/prod/api
  Next steps:
    - [read-only] Image ghcr.io/org/api:v2.3.0 cannot be pulled; check ghcr.io status or the image existence
    - [read-only] If image is correct, verify imagePullSecrets

$ kubectl describe deploy api -n prod | grep Image:
    Image:      ghcr.io/org/api:v2.3.0    # ← the tag in the manifest

$ kubectl get events --field-selector involvedObject.name=api-xxx -n prod | tail -3
... Failed to pull image "ghcr.io/org/api:v2.3.0": ... manifest unknown
```

**Decision:** The image tag is wrong. Either:
- The release ticket has the correct tag (someone typo'd `v2.3.0` for `v2.3.1`) → commit fix, let Argo reconcile
- The image was never built → kick off the CI build job

cub-scout doesn't pick. The operator does, with full evidence in hand.

## Tool boundary

- **Allowed:** doctor / explain / trace / map / scan / patterns detect; kubectl get/describe/logs/events
- **Not allowed:** `kubectl rollout undo`, `kubectl set image`, `kubectl delete pod`, `argocd app sync`, `argocd app rollback`, `flux reconcile`, `helm rollback`, any `cub * create/update/delete`. The triage produces evidence; the rollback / fix is the operator's call.

## Time-pressure tactics

- **Skip the JSON parse** if reading manually — ASCII output of `doctor` and `explain` is denser per-character than JSON
- **Use `--presentation ai`** for LLM-driven triage — uppercase markers and bracket notation are more reliable than free-form text
- **Single-namespace scoping**: `doctor -n <ns>` and `explain <kind>/<name> -n <ns>` keep blast-radius small
- **Save the receipt** if this is going to a postmortem: `cub-scout receipt verify deploy/api -n prod --since <time> --save`. See [`scout-verify`](../scout-verify/SKILL.md).

## References

- [`scout-diagnose`](../scout-diagnose/SKILL.md) — the verb group this skill composes
- [`scout-observe`](../scout-observe/SKILL.md) — for inventory framing
- [`scout-attribute`](../scout-attribute/SKILL.md) — for reading the `cause` field
- [`operator-incident-evidence`](../operator-incident-evidence/SKILL.md) — for the slower, postmortem-shaped variant
- `--presentation` flag: `#359`
- Recent K8s events on explain/trace: `#368`
- Secret evidence on trace: `#328`, `#360`

## Constraints

- This is a **triage** skill, not an incident-management process. Don't treat the loop as a runbook checklist for the whole incident — it's the first 30 seconds.
- cub-scout never mutates. Every rollback / restart / patch / sync is the operator running another tool. The skill produces evidence and structured next-step hints; the operator drives.
- If the evidence is `cause=unknown` or `secrets=unresolved`, the triage may need to escalate. Don't pretend `unknown` is a verdict.

---
> Source: [confighub/cub-scout](https://github.com/confighub/cub-scout) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
