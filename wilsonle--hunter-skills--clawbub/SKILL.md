---
name: clawbub
description: Enable agents to use the Clawhub CLI (clawbub) to install, import, publish, and manage AgentSkills. Use when an agent needs to interact with Clawhub from the command line for skill development, publishing, or syncing. Use when this capability is needed.
metadata:
  author: wilsonle
---

# Clawbub – Clawhub CLI Skill

This skill allows an agent to **safely and correctly use the Clawhub CLI** (`clawbub`, `skills`, `bunx clawhub`) to manage AgentSkills.

## Mental Model
The agent acts as a **release engineer for skills**:
- GitHub repo = source of truth
- Clawhub = distribution registry
- Local install = consumer

Never skip the publish step.

---

## Core Capabilities

### Install a skill from Clawhub
```bash
bunx clawhub@latest install <skill-name> --force
```
Use when syncing local state with the registry.

---

### Import (publish) a skill to Clawhub
```bash
npx skills import <skill-folder>
```
Run from the repo root. This is required before others can install updates.

---

### Install Clawhub tooling
```bash
npx skills add https://github.com/openclaw/openclaw/clawhub -y
```
One-time setup. Avoid interactive mode.

---

## Safe Workflow (Recommended)

1. Modify skill in GitHub repo
2. Commit + push
3. Import into Clawhub (`skills import`)
4. Reinstall locally (`clawhub install --force`)
5. Restart gateway

---

## When NOT to Use This Skill
- Do not run destructive commands without confirmation
- Do not import from untrusted repos
- Do not overwrite skills you don’t own

---

## Golden Rule
If the registry and the repo disagree, **the registry is wrong until you import again**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wilsonle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
