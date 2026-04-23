---
name: coderabbit
description: Use when reviewing uncommitted changes, preparing PRs, checking for security issues, or verifying code quality. Runs AI code reviews via CLI. Covers code review, PR review, security scan, secrets scan. NOT for: runtime debugging (use debugger), test execution (run tests directly).
metadata:
  author: etanhey
---

# CodeRabbit - AI Code Review

Fast AI code reviews via CodeRabbit CLI. Free for open source.

## Repositories

Works in any git repo. Free tier covers open source repos. For private repos, ensure CodeRabbit is configured in the repo settings.

## Quick Commands

```bash
cr review --plain           # Human-readable review
cr review --prompt-only     # For AI agents (minimal tokens)
cr review --type uncommitted # Only unstaged changes
cr review --base main       # Compare against main branch
```

## Workflows

| Workflow | Use Case |
|----------|----------|
| [review](workflows/review.md) | Standard code review |
| [verify](workflows/verify.md) | Quick verification for Ralph V-* stories |
| [security](workflows/security.md) | Security-focused review |
| [accessibility](workflows/accessibility.md) | A11y audit for UI changes |
| [secrets](workflows/secrets.md) | Scan for hardcoded secrets/keys |
| [pr-ready](workflows/pr-ready.md) | Pre-PR comprehensive check |

## Output Modes

| Flag | Best For | Token Usage |
|------|----------|-------------|
| `--plain` | Humans reading in terminal | High |
| `--prompt-only` | AI agents (Ralph, Claude) | Low |
| (default) | Interactive TUI | N/A |

## Integration with Ralph

For V-* verification stories, CodeRabbit runs FIRST as a fast pre-check:

1. `cr review --prompt-only --type committed` - Quick scan
2. If issues found → Fix before Claude verification
3. If clean → Proceed to full Claude verification

This reduces Claude API costs and catches obvious issues fast.

## Evaluator Agent (Weighted Quality Gate)

For high-stakes changes, pair CodeRabbit with the **evaluator agent** (`claude --agent evaluator`) for deeper qualitative scoring:

1. CodeRabbit catches structural issues (bugs, security, style)
2. Evaluator scores on 4 weighted criteria: Functionality (20%), Craft (20%), Design (30%), Originality (30%)
3. Score >= 7.0 required to proceed to merge

The evaluator is deliberately adversarial -- it compensates for LLM optimism bias in code review. See `~/Gits/orchestrator/standards/evaluator-grading.md` for the full grading rubric.

**When to add the evaluator gate:**
- Architecture changes or new module introductions
- Agent-generated code (autonomous work products)
- Changes touching >5 files or crossing module boundaries

## Configuration

Optional `.coderabbit.yaml` in repo root for custom rules:

```yaml
reviews:
  language: en
  path_filters:
    - "!**/*.test.ts"
    - "!**/node_modules/**"
```

## Requirements

- CodeRabbit CLI installed: `curl -fsSL https://cli.coderabbit.ai/install.sh | sh`
- Authenticated: `cr auth login`
- Must run from git repository root

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/etanhey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
