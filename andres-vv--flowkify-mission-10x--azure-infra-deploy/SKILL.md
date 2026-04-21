---
name: azure-infra-deploy
description: Deploy Azure infrastructure (Bicep) using Azure Developer CLI (azd) only; never use az or az deployment for Bicep. Covers local and CI/CD provisioning and deployment. Use when deploying or provisioning Azure infra, editing Bicep, or when the user asks how to deploy infra, run bicep, or provision Azure resources. Use when this capability is needed.
metadata:
  author: andres-vv
---

# Azure infrastructure and deployment

All Bicep/infrastructure deployments **must** use **Azure Developer CLI (azd)**. Do **not** use `az deployment` or other `az` commands to deploy Bicep.

## Rule

- **Use azd** for provisioning and deploying Bicep: `azd provision`, `azd deploy`, `azd up`.
- **Never use** `az deployment group create`, `az bicep build` + `az deployment`, or similar for deploying Bicep.

---

## Project layout (azure.yaml)

Project config lives in **azure.yaml** at the repo root:

- **infra**: `infra.provider` (bicep), `infra.path` (e.g. `infra`), `infra.module` (e.g. `main`). Bicep entry is `<path>/<module>.bicep`.
- **services**: Each key under `services:` is a **service name** used for deploy (e.g. `azd deploy <service-name>`). Read `azure.yaml` to get the exact names; do not hardcode them in instructions.

Example: if `azure.yaml` has `services: { my-app: ... }`, deploy with `azd deploy my-app`.

---

## Deploy infra locally (without full CI/CD)

Use when you want to provision or update infra from your machine without running the full pipeline (build → provision → migrations → deploy).

1. **Prerequisites**
   - Azure Developer CLI: `azd version`.
   - Logged in: `az login` or `azd auth login`.

2. **Select or create environment**
   - Existing: `azd env select <env-name>` (env names are project-specific; common: dev, tst, prd). Config lives under `.azure/`.
   - New: `azd env new <name>` then `azd env select <name>`.

3. **Provision infra**
   - Interactive (first time): `azd provision` (prompts for subscription, location, and any Bicep params).
   - Non-interactive: `azd provision --no-prompt`.
   - This deploys the Bicep in the path from `azure.yaml` and updates the resource group for the selected environment.

4. **Optional: deploy a service**
   - After provision: `azd deploy <service-name> --no-prompt` where `<service-name>` is a key from `azure.yaml` → `services:` (e.g. one service might be the web app). Requires a prior build if the service needs an artifact. For infra-only, skip this.

5. **Summary**
   - Infra only: `azd env select <env>` → `azd provision --no-prompt`.
   - Infra + service: `azd provision --no-prompt` then `azd deploy <service-name> --no-prompt` (get `<service-name>` from `azure.yaml`; ensure build artifact exists if the service needs it).

---

## Local commands (quick reference)

- **Environment**: `azd env list`, `azd env select <name>`, `azd env new <name>`.
- **Provision**: `azd provision` (interactive) or `azd provision --no-prompt`.
- **Deploy a service**: `azd deploy <service-name> --no-prompt` — `<service-name>` from `azure.yaml` → `services:`.
- **Provision + deploy all**: `azd up --no-prompt` (provisions then deploys all services defined in `azure.yaml`).

Always run `azd env select <env>` before provision or deploy so the correct environment (and resource group) is used.

---

## CI/CD pattern

- Pipelines that deploy infra and app should use **azd** (e.g. `Azure/setup-azd@v2`, then `azd provision --no-prompt`, then `azd deploy <service-name> --no-prompt`). Service name(s) come from `azure.yaml`.
- Do **not** replace azd with `az deployment` or raw `az` for Bicep deployment.
- Using `az` for **operational** tasks (e.g. temporary DB firewall rule for migrations, ACR login) is fine; the rule applies only to deploying Bicep/infra.

---

## When to apply

- User asks to deploy infra, provision Azure, run Bicep, or deploy to an environment.
- User asks how to deploy locally without running the full pipeline.
- User suggests using `az deployment` or `az` to deploy Bicep → use azd and the steps above.
- Editing Bicep or `azure.yaml` in a way that affects deployment → ensure any instructions or workflows use azd and service names from `azure.yaml`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andres-vv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
