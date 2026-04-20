---
name: deploy-and-release
description: Deploy and release to production. Coordinates release PRs, QA verification, staging, and production deployment. Customize for your deployment pipeline. Use when this capability is needed.
metadata:
  author: endaoment
---

# /deploy-and-release

Release merged PRs to production.

## Phase Intent

This phase handles the full release lifecycle: release candidate PRs, CI monitoring, QA verification, staging, and production deployment. Human checkpoints apply at QA approval and pipeline approvals.

> **Customize this phase** for your deployment pipeline. If you have a release-manager agent, this phase wraps it. Otherwise, Gandalf coordinates the release directly.

## Constraints

1. NEVER skip human checkpoints -- QA approval required before production.
2. NEVER deploy without CI green on the release branch.
3. If the release process stalls, escalate to the user.

## Workers

| Worker  | `subagent_type`  | Mission                        |
| ------- | ---------------- | ------------------------------ |
| Gandalf | (orchestrator)   | Coordinate the release         |
| Smeagol | `smeagol-the-pm` | QA issue creation and tracking |

> **Tip**: If your project has a dedicated release-manager agent, add it here as the primary worker and make this phase a thin wrapper.

## Done Criteria

Complete when: production is deployed and a report has been delivered to the user.

## Output

```text
## [Deploy & Release] Complete

### Deployed
- {App/Service}: {version} to production

### Summary
{What was released and any notable changes}

### Next Steps
- Monitor production for issues
- Update release notes if needed
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/endaoment) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
