---
name: github-workflow-automation
description: Automate GitHub workflows with AI-powered assistance for PR reviews, issue triage, CI/CD pipelines, and Git operations. Use when setting up automated code reviews, implementing issue labeling, creating smart CI/CD workflows, building deployment validation, automating Git operations, or configuring on-demand AI assistance bots. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# 🔧 GitHub Workflow Automation

Automate GitHub workflows with AI-powered assistance using GitHub Actions, inspired by [Gemini CLI](https://github.com/google-gemini/gemini-cli) and modern DevOps practices.

## Overview

This skill provides patterns for integrating AI into GitHub workflows to automate code reviews, issue management, CI/CD pipelines, and Git operations. It focuses on practical, production-ready GitHub Actions configurations that leverage Claude/AI for intelligent automation.

## Core Capabilities

- **AI-Powered PR Reviews**: Automated code review with structured feedback
- **Issue Triage**: Auto-label, categorize, and manage incoming issues
- **CI/CD Integration**: Smart test selection, deployment validation, rollback automation
- **Git Operations**: Automated rebase, cherry-pick, and branch cleanup
- **On-Demand Assistance**: @mention bots for interactive help in PRs and issues

## Detailed Workflow Guides

Choose the appropriate reference based on your automation needs:

### [PR Review Automation](references/pr-review-automation.md)

**Use when**: Setting up automated code review workflows, implementing review patterns, creating focused filters.

Covers:

- Complete GitHub Actions workflow for AI-powered PR reviews
- Structured review comment patterns
- File type filtering and context inclusion
- Security best practices

### [Issue Triage](references/issue-triage.md)

**Use when**: Automating issue labeling, managing stale issues, implementing first-response templates.

Covers:

- Auto-labeling based on issue analysis
- Triage prompt patterns
- Stale issue management workflows
- First-response automation

### [CI/CD Integration](references/cicd-integration.md)

**Use when**: Building smart CI/CD pipelines, implementing deployment validation, creating rollback automation.

Covers:

- Smart test selection based on file changes
- AI-powered deployment risk assessment
- Automated rollback workflows with notifications

### [Git Operations](references/git-operations.md)

**Use when**: Automating Git workflows, handling rebase/cherry-pick operations, managing branch lifecycle.

Covers:

- Comment-triggered auto-rebase
- AI-assisted cherry-pick with conflict resolution
- Weekly branch cleanup automation

### [Repository Configuration](references/repository-configuration.md)

**Use when**: Setting up CODEOWNERS, configuring branch protection, implementing @mention bots.

Covers:

- CODEOWNERS file patterns
- Branch protection via GitHub API
- @mention bot for on-demand assistance
- Available commands reference

## Quick Start

**1. Choose your automation type** from the list above

**2. Read the appropriate reference guide** with complete workflow examples

**3. Adapt to your repository**:

- Replace placeholders (repo names, team names)
- Configure secrets (AI API keys)
- Adjust thresholds and rules

**4. Test in staging** before production deployment

## Best Practices

### Security

- Store AI API keys in GitHub Secrets (`ANTHROPIC_API_KEY`, etc.)
- Use minimal permissions in workflows (`contents: read`, `pull-requests: write`)
- Validate all inputs before processing
- Never expose sensitive data in workflow logs

### Performance

- Use path filters to skip unnecessary workflows
- Implement smart test selection to reduce CI time
- Cache dependencies appropriately
- Consider self-hosted runners for heavy workloads

### Reliability

- Add timeouts to all jobs (default: 360 min may be too long)
- Handle API rate limits gracefully
- Implement retry logic for transient failures
- Maintain rollback procedures for all automations

## Common Patterns

**Trigger on PR events**:

```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened]
```

**Use outputs between jobs**:

```yaml
jobs:
  analyze:
    outputs:
      result: ${{ steps.step-id.outputs.value }}
  
  use:
    needs: analyze
    steps:
      - run: echo "${{ needs.analyze.outputs.result }}"
```

**Conditional execution**:

```yaml
if: contains(github.event.comment.body, '/command')
```

## Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GitHub REST API](https://docs.github.com/en/rest)
- [Anthropic API Documentation](https://docs.anthropic.com)
- [CODEOWNERS Syntax](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners)

## Outputs & Deliverables

- **Primary Output**: GitHub Actions workflows (`.github/workflows/*.yml`)
- **Secondary Output**: Automation scripts, CODEOWNERS configuration, documentation
- **Success Criteria**: Workflows execute successfully in dry-run, automation performs expected actions
- **Quality Gate**: `verification-before-completion` evidence (CI logs), `guardian` review for security-sensitive automations

## Constraints

**Technical Constraints**:

- Do not run production-changing workflows without explicit approval and rollback plan
- Respect GitHub API rate limits (5000 requests/hour for authenticated requests)
- Workflows must complete within timeout limits (default 360 min, recommend <60 min)

**Scope Constraints**:

- This skill produces GitHub Actions workflows and automation configurations
- Infrastructure provisioning (Terraform, cloud resources) should use `ops-manager` skill
- Database migrations and schema changes should involve appropriate data skills

**Governance Constraints**:

- Workflows must respect CODEOWNERS and require appropriate approvals
- Security-sensitive changes require manual review
- Production deployments should use GitHub Environments with protection rules

## Common Pitfalls

- **Secrets in Logs**: Accidentally exposing secrets via `echo` or error messages → Always use GitHub Secrets and mask outputs
- **No Dry-Run Testing**: Running automation against production immediately → Test in staging branch/environment first
- **Missing Rollback**: Automating destructive operations without recovery → Document and practice rollback before deploying
- **Flaky Workflows**: Race conditions or network timeouts → Add retries and appropriate timeouts to all steps
- **Over-Permissioning**: Giving workflows more access than needed → Use minimal scopes (read-only unless write required)
- **Silent Failures**: Workflows fail without notifications → Always configure failure alerts (Slack, email, etc.)
- **Ignoring Rate Limits**: Hitting GitHub API limits causing automation failures → Implement exponential backoff and caching

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Workflow Design | Requirements | GitHub Actions YML | Design automation workflows |
| PR Automation | Code changes | `guardian` | Trigger reviews on PRs |
| Issue Triage | GitHub issues | Labels & assignments | Auto-categorize issues |
| CI/CD | Test results | Deployment | Run tests, validate, deploy |
| Verification | Workflow logs | `verification-before-completion` | Evidence of automation success |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
