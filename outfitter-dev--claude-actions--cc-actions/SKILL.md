---
name: cc-actions
description: | Use when this capability is needed.
metadata:
  author: outfitter-dev
---

# Claude Code GitHub Actions

Configure automation using [`anthropics/claude-code-action`](https://github.com/anthropics/claude-code-action) with event routing, subagents, hooks, and MCP integration.

## Quick Start

```yaml
name: Claude Code
on:
  issue_comment: { types: [created] }
  pull_request: { types: [opened, synchronize] }
  issues: { types: [opened, labeled] }

permissions:
  contents: write
  pull-requests: write
  issues: write

jobs:
  claude:
    if: github.actor != 'github-actions[bot]'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

## Core Concepts

| Concept | Purpose | Details |
|---------|---------|---------|
| **Event Routing** | Route GitHub events to behaviors | [patterns/event-routing.md](patterns/event-routing.md) |
| **Subagents** | Parallel execution in isolated contexts | [patterns/subagents.md](patterns/subagents.md) |
| **Hooks** | Quality gates and automation | [patterns/hooks.md](patterns/hooks.md) |
| **MCP Servers** | External tool integration | [official-docs/configuration.md](official-docs/configuration.md) |
| **Permissions** | Tool and access control | [patterns/permissions.md](patterns/permissions.md) |
| **Security** | Fork-safe PR handling | [patterns/security.md](patterns/security.md) |
| **Reusable Workflows** | Organization-wide sharing | [patterns/reusable-workflows.md](patterns/reusable-workflows.md) |
| **Performance** | Optimization patterns | [patterns/performance.md](patterns/performance.md) |
| **Skills** | Domain expertise | [patterns/skill-usage.md](patterns/skill-usage.md) |
| **Official Docs** | Upstream documentation | [official-docs/](official-docs/) |

## Key Patterns

### Event Routing

```yaml
jobs:
  pr-comment:
    if: github.event_name == 'issue_comment' && github.event.issue.pull_request
  issue-comment:
    if: github.event_name == 'issue_comment' && !github.event.issue.pull_request
  label-triggered:
    if: contains(github.event.issue.labels.*.name, 'claude-implement')
```

### Subagents

```yaml
prompt: |
  Delegate to 3 parallel subagents:
  1. Security Agent: OWASP Top 10, auth checks
  2. Performance Agent: N+1 queries, memory leaks
  3. Style Agent: Naming, documentation, tests
```

### Hooks

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{ "type": "command", "command": "npx prettier --write $(jq -r '.tool_input.file_path')" }]
    }],
    "Stop": [{
      "hooks": [{ "type": "command", "command": "npm run lint && npm test" }]
    }]
  }
}
```

### Performance

```yaml
concurrency:
  group: claude-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

on:
  pull_request:
    paths: ['src/**', '!**/*.md']
```

## Tuning Reference

| Scenario | Max Turns | Model | Timeout |
|----------|-----------|-------|---------|
| Triage/labeling | 2-3 | sonnet | 5 min |
| Code review | 8-12 | sonnet | 15 min |
| Implementation | 15-20 | sonnet | 20 min |
| Security audit | 10-15 | opus | 30 min |

## Critical Gotchas

1. **`--allowedTools` vs `--disallowedTools`**
   - `allowedTools` only skips prompts—does NOT restrict tools
   - Use `disallowedTools` to actually block: `--disallowedTools Bash,Run,Edit`

2. **MCP + Subagents**
   - MCP tools do NOT work in background subagents
   - Always run MCP operations in foreground

3. **Fork Safety**
   - `pull_request` event = safe (read-only token, no secrets)
   - `pull_request_target` = dangerous if you checkout fork code
   - Never: `checkout ref: ${{ github.event.pull_request.head.sha }}` with secrets

4. **Hook Exit Codes**
   - `0` = continue
   - `2` = block, feed stderr to Claude
   - Other = log error, don't block

5. **Subagent Limits**
   - ~10 concurrent subagents max
   - Additional tasks queue

## Examples

See [examples/](examples/) for 14 production-ready use cases:

| # | Use Case | Techniques |
|---|----------|------------|
| 1 | Parallel Review Pipeline | Subagents, concurrency |
| 2 | ChatOps Command Router | Event routing, comment parsing |
| 3 | Issue-to-Implementation | Label triggers, Stop hooks |
| 4 | Compliance Firewall | PreToolUse blocking, PostToolUse formatting |
| 5 | Security Review | Path filtering, tool restrictions |
| 6 | Monorepo Support | dorny/paths-filter, language skills |
| 7 | Ticket Sync | MCP servers (foreground!) |
| 8 | Fork-Safe Review | pull_request event, disallowedTools |
| 9 | Scheduled Maintenance | Cron, parallel subagents |
| 10 | Documentation Sync | Scheduled, MCP |
| 11 | Dependency Review | Bot filtering, semver skill |
| 12 | Release Notes | Release event, categorization |
| 13 | Reusable Workflow | workflow_call, inputs/outputs |
| 14 | CLAUDE.md Pattern | Project context, conventions |

## Patterns

- [Event Routing](patterns/event-routing.md) - Conditions, label routing, author associations
- [Subagents](patterns/subagents.md) - Creation, parallel execution, built-in types
- [Hooks](patterns/hooks.md) - All events, exit codes, JSON parsing, examples
- [Permissions](patterns/permissions.md) - Tool control layers, known issues
- [Security](patterns/security.md) - Fork handling, external contributors, safe patterns
- [Reusable Workflows](patterns/reusable-workflows.md) - workflow_call, composite actions
- [Performance](patterns/performance.md) - Concurrency, caching, path filtering
- [Skills](patterns/skill-usage.md) - Domain expertise, skill structure, hooks

## Official Docs

Synced from [anthropics/claude-code-action](https://github.com/anthropics/claude-code-action):

- [Configuration](official-docs/configuration.md) - MCP servers, settings, custom tools
- [Security](official-docs/security.md) - API key protection, commit signing
- [Usage](official-docs/usage.md) - Inputs, outputs, migration guide

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/outfitter-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
