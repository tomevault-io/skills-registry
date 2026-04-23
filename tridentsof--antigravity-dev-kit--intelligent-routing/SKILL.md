---
name: intelligent-routing
description: name: intelligent-routing Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: intelligent-routing
description: Auto-select best agent for each request
---

# Intelligent Routing

> Automatically select the best agent for each task.

---

## Routing Matrix

| Keywords | Agent |
|----------|-------|
| vue, component, pinia, frontend | frontend-specialist |
| api, controller, service, c#, aspnet | backend-specialist |
| sql, database, schema, query | database-architect |
| deploy, pipeline, aks, kubernetes | devops-engineer |
| security, keyvault, audit, owasp | security-auditor |
| test, xunit, vitest, coverage | test-engineer |
| bug, error, fix, debug | debugger |
| performance, slow, metrics | performance-optimizer |
| docs, readme, documentation | documentation-writer |
| analyze, explore, overview | explorer-agent |
| plan, design, architecture | project-planner |
| complex, multi-domain | orchestrator |

---

## Routing Protocol

### Step 1: Analyze Request
Identify keywords and domain indicators.

### Step 2: Match Agent
Use routing matrix to find best match.

### Step 3: Check Complexity
- Single domain → Direct to specialist
- Multi-domain → Route to orchestrator

### Step 4: Inform User
```markdown
🤖 **Applying `@[agent-name]`...**
```

---

## Multi-Domain Detection

| Indicators | Action |
|------------|--------|
| "full-stack" | orchestrator |
| Frontend + Backend | orchestrator |
| Multiple file types | orchestrator |

---

## Override Rules

| User Says | Action |
|-----------|--------|
| @agent-name | Use specified agent |
| /command | Follow command flow |
| "don't ask" | Skip Socratic Gate |

---

## Fallback

If no clear match:
1. Ask user to clarify
2. Default to `project-planner` for planning
3. Default to `orchestrator` for execution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
