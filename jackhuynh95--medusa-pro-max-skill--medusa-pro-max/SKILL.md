---
name: medusa-pro-max
description: Medusa.js workflow intelligence. Returns processing, refunds, order management, e-commerce workflows. Actions: design, implement, search, generate, review, optimize, debug, structure, architect, refactor workflow code. Components: workflow steps, compensation logic, hooks, modules, services, API routes, subscribers. Patterns: idempotent steps, reversible workflows, long-running tasks, saga pattern, error handling, retry logic. Topics: Medusa.js, workflow API, step functions, module architecture, database transactions, event-driven design, TypeScript, PostgreSQL, Redis. (project) Use when this capability is needed.
metadata:
  author: jackhuynh95
---

# Medusa.js Returns Workflow Skill

## Prerequisites

Check if Python is installed:

```bash
python3 --version || python --version
```

If Python is not installed, install it based on user's OS:

**macOS:**
```bash
brew install python3
```

**Ubuntu/Debian:**
```bash
sudo apt update && sudo apt install python3
```

**Windows:**
```powershell
winget install Python.Python.3.12
```

---

## Description
This skill assists in designing and implementing a Returns Workflow for Medusa.js, following official best practices and architectural patterns.

## Capabilities
- **Best Practices Lookup:** Quickly find guidelines for Workflows, Modules, and Configuration.
- **Documentation Search:** Query the knowledge base for Medusa v2 patterns and references.
- **Workflow Design:** Step-by-step guidance on designing idempotent, reversible workflows.
- **Implementation Support:** Generate boilerplate and logic for Medusa Workflows.

## Tools
- `search.py`: Search the best practices database (CSV).
    - Usage: `python3 .claude/skills/medusa-pro-max/scripts/search.py "keyword"`
    - Note: Uses `medusa-v2-docs.csv` by default.

## Data Sources
- `.claude/skills/medusa-pro-max/data/medusa-best-practices.csv`: Curated list of best practices derived from official documentation.
- `.claude/skills/medusa-pro-max/data/medusa-v2-docs.csv`: Comprehensive documentation, implementation rules, and architectural standards in structured CSV format.
- `.claude/skills/medusa-pro-max/data/loyalty-plugin.csv`: Loyalty plugin features including gift cards and store credit.

## Additional Skills
- [SKILL-LOYALTY.md](./SKILL-LOYALTY.md): Medusa Loyalty Plugin with gift cards and store credit functionality.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jackhuynh95) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
