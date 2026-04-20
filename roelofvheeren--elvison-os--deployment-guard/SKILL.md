---
name: guarding-deployments
description: Performs pre-deployment checks ensuring code quality and environment readiness. Use when the user mentions deploying, shipping, or production releases. Use when this capability is needed.
metadata:
  author: roelofvheeren
---

# Guarding Deployments

## When to use this skill
- Before pushing to production or staging branches.
- When the user asks "is it safe to deploy?".
- To verify environment variables and secrets are present.

## Workflow

1.  **Preparation**
    - [ ] Identify target environment (e.g., `production`, `staging`).
    - [ ] List required environment variables.
2.  **Verification**
    - [ ] Run linting and type checks.
    - [ ] Execute test suite with `npm test`.
    - [ ] Verify environment variables using `scripts/check-env.sh`.
3.  **Execution**
    - [ ] If all checks pass, proceed with deployment command.
    - [ ] If any check fails, block deployment and report errors.

## Instructions

### Linting & Tests
Ensure the project is in a clean state:
```bash
# Run linting
npm run lint

# Run unit tests
npm test
```

### Environment Variable Check
Use the provided script to ensure required keys are set:
```bash
./scripts/check-env.sh .env.production DB_URL API_KEY
```

## Resources
- [check-env.sh](scripts/check-env.sh)
- [Example .env](examples/env.example)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roelofvheeren) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
