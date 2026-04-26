---
name: devops-packaging-service-flow
description: Step-by-step flow for service packaging scaffolding. Keywords: packaging, service, flow. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Flow: HTTP Service Packaging

This doc contains the step-by-step flow. For inputs, tools, outputs, and safety, see
/.system/skills/ssot/repo/scaffolding/devops-packaging-service/SKILL.md.

---

## Step-by-Step Flow (AI + Human)

### Step 1: Collect Parameters (AI -> Human)

AI asks human for:
1. Service name for the image
2. Which module to package
3. Service port
4. Optional: base image, registry URL

**Human checkpoint**: Confirm parameters before proceeding.

### Step 2: Validate Inputs (AI)

AI validates:
- Module exists and has source code
- Service name follows naming conventions
- Port is valid

### Step 3: Preview Changes (AI)

AI runs orchestrator with `--dry-run`:
```bash
python scripts/devops/scaffold/devops_packaging_service.py   --service-name "<service_name>"   --module-id "<module_id>"   --port 8080   --dry-run
```

AI presents `planned_changes` to human.

**Human checkpoint**: Review and approve planned changes.

### Step 4: Execute Scaffold (AI)

AI runs orchestrator without `--dry-run`:
```bash
python scripts/devops/scaffold/devops_packaging_service.py   --service-name "<service_name>"   --module-id "<module_id>"   --port 8080
```

### Step 5: Review Generated Files (AI + Human)

AI presents generated files for review:
- Dockerfile
- Build script
- Packaging configuration

**Human checkpoint**: Review generated files and customize if needed.

### Step 6: Record in Workdocs (AI)

AI records scaffold execution in `/ops/packaging/workdocs/`:
- Service name and module
- Configuration decisions
- Follow-up tasks (build, push, deploy)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
