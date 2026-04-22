---
name: ticket-creation
description: Triage and create GitHub issues from user-submitted support tickets. This skill should be used when converting raw user support requests into structured, actionable GitHub issues with proper labels, priority, templates, and formatting. Triggers on requests like "create a ticket", "triage this support request", "file an issue from this feedback", or any support-to-issue conversion task. Use when this capability is needed.
metadata:
  author: vevarunsharma
---

# Ticket Creation

Convert raw user support tickets into well-structured, triageable GitHub issues using the Copilot SDK and GitHub REST API.

## Overview

This skill is used by the Octocat Support App to parse unstructured user input (support form submissions) and produce actionable GitHub issues that can be triaged by humans or automation agents. The Copilot SDK is the orchestration layer — it receives the raw ticket, applies triage rules, and calls a custom tool to create the issue.

## Triage Rules

### 1. Title Formatting

- **Always** start with a type prefix: `[Bug]`, `[Feature]`, `[Question]`, `[Docs]`, `[Security]`
- Be specific and actionable — avoid vague titles like "Something is broken"
- Keep under **72 characters**
- Use **imperative mood** for bugs and features: "Fix X", "Add Y", not "X is broken"

**Examples:**

- ✅ `[Bug] Fix SSO login crash on redirect`
- ✅ `[Feature] Add CSV export to dashboard`
- ✅ `[Question] How to configure webhooks for org repos`
- ❌ `bug with login` (no prefix, vague)
- ❌ `I think there might be an issue with the deployment pipeline when using custom runners` (too long)

### 2. Category → Label Mapping

| User Category | GitHub Label    |
| ------------- | --------------- |
| bug           | `bug`           |
| feature       | `enhancement`   |
| question      | `question`      |
| docs          | `documentation` |
| security      | `security`      |

### 3. Priority → Label Mapping

| User Priority | GitHub Label        | SLA Expectation       |
| ------------- | ------------------- | --------------------- |
| critical      | `priority:critical` | Immediate (< 4 hours) |
| high          | `priority:high`     | Same day (< 24 hours) |
| medium        | `priority:medium`   | This week             |
| low           | `priority:low`      | Backlog               |

### 4. Additional Labels (Auto-Inferred)

Apply these when the content warrants it:

| Label                 | When to Apply                                   |
| --------------------- | ----------------------------------------------- |
| `needs-reproduction`  | Bug report lacks clear reproduction steps       |
| `needs-clarification` | Description is too vague to act on              |
| `good first issue`    | Simple, well-scoped task suitable for newcomers |
| `help wanted`         | Community-friendly requests                     |
| `breaking-change`     | Feature involves breaking existing behavior     |
| `performance`         | Related to speed, latency, or resource usage    |
| `accessibility`       | Related to a11y improvements                    |

## Body Templates

Use the templates in [references/templates.md](references/templates.md) based on the ticket category. Each template provides a consistent structure that makes issues scannable, triageable, and actionable.

## Copilot SDK Integration

### Architecture

```
User Form → POST /api/tickets → Zod Validation → CopilotClient Session
  → System Prompt (triage rules) + Custom Tool (create_github_issue)
  → Model triages & calls tool → GitHub REST API → Issue Created
  → Return issue URL/number/labels to client
```

### Custom Tool: `create_github_issue`

The tool accepts:

- `title` (string) — Formatted issue title with type prefix
- `body` (string) — Full markdown body using the appropriate template
- `labels` (string[]) — Array of label strings

The tool handler calls the GitHub REST API (`POST /repos/{owner}/{repo}/issues`) and returns the created issue URL.

### Session Configuration

- **Model:** `gpt-4o` (or configurable)
- **System Message Mode:** `append` (preserves SDK guardrails)
- **Streaming:** Disabled (synchronous tool execution)
- **Cleanup:** Always use `try/finally` with `session.destroy()` and `client.stop()`

## Quality Checklist

Before creating an issue, verify:

- [ ] Title has correct type prefix
- [ ] Title is under 72 characters and actionable
- [ ] Body uses the correct template for the category
- [ ] Reporter info (name, email) is included
- [ ] At least one category label is applied
- [ ] Priority label matches user selection
- [ ] Description has been restructured (not copy-pasted verbatim)
- [ ] Any missing information is flagged with `needs-` labels

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vevarunsharma) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
