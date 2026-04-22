---
name: ci-cd-setup
description: Automation instructions for testing and deployment. Use when setting up GitHub Actions or deployment pipelines. Use when this capability is needed.
metadata:
  author: wynautbhav
---

# CI/CD Setup Skill

## Workflow Standards
1. **Lint & Test**: Run linter and unit tests on every Pull Request. Block merge if they fail.
2. **Build Check**: Ensure the project compiles (`npm run build`) before merging.
3. **Auto-Deploy**: Automatically deploy the `main` branch to the production environment (Vercel/Netlify/AWS).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wynautbhav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
