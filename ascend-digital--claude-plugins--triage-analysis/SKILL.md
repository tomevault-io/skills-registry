---
name: triage-analysis
description: Codebase-aware Jira ticket triage analysis. Reads a Jira ticket via Atlassian MCP, explores relevant repositories via GitHub MCP, and produces a three-section report (understanding of the request, how the system currently works, clarifying questions) as JSON. Use when this capability is needed.
metadata:
  author: ascend-digital
---

# Triage Analysis

Analyze a Jira ticket against the relevant codebase(s) and produce a three-section triage
report.

## Workflow

### 1. Read the Jira Ticket

Use the Atlassian MCP `getJiraIssue` to read the full Jira ticket — get the summary,
description, acceptance criteria, comments, and any other relevant fields.

**Important**: Jira Service Desk tickets store reporter responses in custom fields, not just
the `description` field. Always include known Service Desk form fields in the fetch. See
`references/memory.md` under "Service Desk Custom Fields" for field IDs per project.

If the `description` is empty and no custom fields are recorded for this project, fetch ALL
fields (omit the `fields` parameter), scan for content-bearing `customfield_*` entries,
filter out boilerplate, and record discoveries in `references/memory.md`.

### 2. Deep Codebase Exploration

Use the GitHub MCP to perform a deep exploration of ALL the primary repositories:

- Read the README and directory structure to understand each project's layout
- Trace through the code comprehensively to understand architecture and abstractions
- Identify modules, services, and files most relevant to what the ticket describes
- Read key files to understand the current implementation in detail
- If you discover references to other repositories in the repo mappings, or shared
  libraries/upstream services, explore those too via the GitHub MCP

Only use read-only GitHub MCP tools. See `references/github-safety.md`.

### 3. Produce the Report

Produce a markdown report with THREE sections:

a. **"## Understanding of the Request"** — summarise what you understand the ticket is
   asking for, including the goal, scope, and any constraints mentioned

b. **"## How the System Currently Works"** — a business-friendly explanation of how the
   relevant parts work today, using diagrams (Unicode box-drawing) where helpful

c. **"## Clarifying Questions"** — if applicable, a numbered list of questions for the
   ticket reporter about ambiguities, missing details, or edge cases

### 4. Return as JSON

Return the output as JSON (and nothing else) in this format:

```json
{
  "understanding_of_request": "## Understanding of the Request\n\n...",
  "how_system_works": "## How the System Currently Works\n\n...",
  "clarifying_questions": "## Clarifying Questions\n\n1. ..."
}
```

Each value contains the full markdown content for that section, including the `##` heading.

## Memory

Read `references/memory.md` for:

- **Entitlement Map** — domain-to-repo mappings
- **Repository Map** — repo relationships and dependencies
- **Service Desk Custom Fields** — known Jira field IDs per project
- **Domain Glossary** — organisation-specific terms

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ascend-digital) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
