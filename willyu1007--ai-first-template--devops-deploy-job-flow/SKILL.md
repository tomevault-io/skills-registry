---
name: devops-deploy-job-flow
description: Step-by-step flow for job deployment scaffolding. Keywords: deploy, job, flow. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Flow: Job Deployment

This doc contains the step-by-step flow. For inputs, tools, outputs, and safety, see
/.system/skills/ssot/repo/scaffolding/devops-deploy-job/SKILL.md.

---

## Step-by-Step Flow (AI + Human)

### Step 1: Collect Parameters (AI -> Human)

AI asks human for:
1. Job name (must match packaging)
2. Target environment
3. Schedule (if recurring)
4. Optional: resource limits, environment variables, secrets

**Human checkpoint**: Confirm parameters before proceeding.

### Step 2: Validate Inputs (AI)

AI validates:
- Packaging exists for the job
- Environment is valid
- Schedule expression is valid (if provided)

### Step 3: Preview Changes (AI)

AI runs orchestrator with `--dry-run`:
```bash
python scripts/devops/scaffold/devops_deploy_job.py   --job-name "<job_name>"   --environment "<environment>"   --schedule "0 2 * * *"   --dry-run
```

AI presents `planned_changes` to human.

**Human checkpoint**: Review and approve planned changes.

### Step 4: Execute Scaffold (AI)

AI runs orchestrator without `--dry-run`:
```bash
python scripts/devops/scaffold/devops_deploy_job.py   --job-name "<job_name>"   --environment "<environment>"   --schedule "0 2 * * *"
```

### Step 5: Review Generated Files (AI + Human)

AI presents generated files for review:
- Kubernetes CronJob manifest (or equivalent)
- Environment configuration
- Deployment scripts

**Human checkpoint**: Review generated files and customize if needed.

### Step 6: Record in Workdocs (AI)

AI records scaffold execution in `/ops/deploy/workdocs/`:
- Job and environment
- Schedule configuration
- Deployment checklist
- Monitoring notes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
