---
name: devops-deploy-service-flow
description: Step-by-step flow for service deployment scaffolding. Keywords: deploy, service, flow. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Flow: HTTP Service Deployment

This doc contains the step-by-step flow. For inputs, tools, outputs, and safety, see
/.system/skills/ssot/repo/scaffolding/devops-deploy-service/SKILL.md.

---

## Step-by-Step Flow (AI + Human)

### Step 1: Collect Parameters (AI -> Human)

AI asks human for:
1. Service name (must match packaging)
2. Target environment
3. Number of replicas
4. Optional: resource limits, environment variables, secrets

**Human checkpoint**: Confirm parameters before proceeding.

### Step 2: Validate Inputs (AI)

AI validates:
- Packaging exists for the service
- Environment is valid
- Resource specifications are reasonable

### Step 3: Preview Changes (AI)

AI runs orchestrator with `--dry-run`:
```bash
python scripts/devops/scaffold/devops_deploy_service.py   --service-name "<service_name>"   --environment "<environment>"   --replicas 2   --dry-run
```

AI presents `planned_changes` to human.

**Human checkpoint**: Review and approve planned changes.

### Step 4: Execute Scaffold (AI)

AI runs orchestrator without `--dry-run`:
```bash
python scripts/devops/scaffold/devops_deploy_service.py   --service-name "<service_name>"   --environment "<environment>"   --replicas 2
```

### Step 5: Review Generated Files (AI + Human)

AI presents generated files for review:
- Kubernetes manifests (or equivalent)
- Environment configuration
- Deployment scripts

**Human checkpoint**: Review generated files and customize if needed.

### Step 6: Record in Workdocs (AI)

AI records scaffold execution in `/ops/deploy/workdocs/`:
- Service and environment
- Configuration decisions
- Deployment checklist
- Rollback procedures

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
