---
name: pr-review-multi-model
description: Multi-model PR review using the 3 Amigos pattern: Claude Sonnet (architect), Gemini 2.5 Pro (bug hunter), DeepSeek (DX reviewer), orchestrated via Mastra. Use when reviewing a PR that needs multi-perspective analysis, when a single-model review has missed bugs before, or when the PR touches critical paths (auth, data layer, proto). Do NOT use Claude Code Max OAuth for these — API keys only, due to ToS. See docs/RATIONALE.md for the design decision. Use when this capability is needed.
metadata:
  author: toru-oizumi
---

# Multi-Model PR Review (3 Amigos)

A PR review agent that runs three specialized reviewers in parallel, each on a different model, then merges their findings into a single structured review comment. The design rationale is in `docs/RATIONALE.md`.

## When to Use

- Reviewing PRs that touch critical paths (auth, data layer, proto schemas, billing)
- Reviewing PRs from team members when you want a second opinion before merging
- Self-reviewing your own PR before asking a human to look
- Post-incident review (re-review a PR that caused a bug to learn what was missed)

## When NOT to Use

- Trivial PRs (typo fix, readme update, dependency bump) — the cost isn't worth it
- When you don't have API keys configured for all three providers
- When the PR contains secrets or production data (use local review instead)

## The 3 Amigos

| Role | Model | Focus |
| --- | --- | --- |
| **Architect Reviewer** | Claude Sonnet (API) | Design principles, dependency direction, layer violations, abstraction level |
| **Bug Hunter** | Gemini 2.5 Pro (API) | Race conditions, null/nil handling, edge cases, security, error swallowing |
| **DX Reviewer** | DeepSeek Chat (API) | Naming, testability, readability, test coverage, public API clarity |

Each runs independently on the full PR diff + repo context. They don't see each other's reviews (by design — avoid groupthink).

## Architecture

```
                ┌─────────────────────┐
                │  GitHub webhook /   │
                │  /harness:review-pr │
                └──────────┬──────────┘
                           │
                    ┌──────▼──────┐
                    │   Mastra    │  (Supervisor)
                    │ Orchestrator│
                    └──────┬──────┘
                           │ parallel
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
   ┌────────────┐   ┌────────────┐   ┌────────────┐
   │  Architect │   │ Bug Hunter │   │  DX Review │
   │  (Sonnet)  │   │ (Gemini)   │   │ (DeepSeek) │
   └──────┬─────┘   └──────┬─────┘   └──────┬─────┘
          │                │                │
          └────────────────┼────────────────┘
                           ▼
                    ┌────────────┐
                    │   Merger   │ (Claude Sonnet)
                    │ + Dedup    │
                    └──────┬─────┘
                           ▼
                  Structured review comment
```

## Why API keys, not Claude Max

Claude Max plan OAuth inside a third-party orchestrator (Mastra server, GitHub Actions) is a ToS grey area. The plugin uses Anthropic API keys server-side to avoid this risk. Claude Max is reserved for local Claude Code use only.

## Setup

### 1. Configure API keys

Create `.env` in the Mastra project (NOT in this plugin's repo):

```bash
ANTHROPIC_API_KEY=sk-ant-...
GEMINI_API_KEY=...
DEEPSEEK_API_KEY=...
GITHUB_TOKEN=ghp_...
```

### 2. Deploy the Mastra server

The plugin does not ship the Mastra server itself — that lives in your own repo (one per deployment). A minimal starter is in the [Mastra github-pr-code-review-agent template](https://github.com/mastra-ai/mastra/tree/main/examples).

Recommended deployment target: GitHub Actions (simplest, already authenticated to your repo) or ECS Fargate (if you want it always-on for faster response).

### 3. Wire the GitHub webhook

On PR open / synchronize → POST the PR number to the Mastra server endpoint. The server pulls the diff, runs the 3 Amigos, and posts a review comment back via `GITHUB_TOKEN`.

## Running locally

Once the Mastra server is reachable:

```
/harness:review-pr <PR-number>
```

Output format: a single markdown comment with three sections:

```markdown
## Multi-Model Review

### 🏛 Architect (Claude Sonnet)
- ...

### 🐛 Bug Hunter (Gemini 2.5 Pro)
- ...

### ✨ DX Reviewer (DeepSeek)
- ...

### 🔍 Consolidated Top Issues
1. ... (mentioned by 2+ reviewers — high confidence)
2. ...
```

## Cost estimation

For a typical 500 LOC PR:
- Architect (Sonnet, ~40k in / ~3k out): ~$0.18
- Bug Hunter (Gemini 2.5 Pro, same): ~$0.05
- DX Reviewer (DeepSeek, same): ~$0.003
- Merger (Sonnet, ~15k in / ~2k out): ~$0.08
- **Total: ~$0.31 per PR**

For small PRs (100 LOC): ~$0.10. Cheap relative to human time.

## Merger: Validation Pass

After collecting findings from all 3 Amigos, the Merger runs a **Validation Pass** before synthesis. For each finding, a lightweight sub-agent checks whether the issue is real and significant in the context of the full diff.

### Confidence Scoring

| Score | Meaning | Action |
|-------|---------|--------|
| ≥ 80 | Real issue, significant | Include in output |
| 50–79 | Possible issue, minor | Include as "Note" only |
| < 50 | Likely false positive | Discard silently |

**Auto-discard without scoring:**
- Issues that existed before this PR (not introduced by the diff)
- Violations already suppressed in CLAUDE.md
- Items a senior engineer would not raise in a real review

## Key learnings

- **Multi-agent error amplification**: unstructured multi-agent networks can amplify errors (per Google DeepMind 2025 research). This is why the 3 Amigos run in **isolated** contexts and a separate Merger reconciles them, rather than letting them debate.
- **Models disagree — that's the point**: if two reviewers flag the same issue, it's probably real. If only one flags it, treat it as a hint.
- **Don't post comments inline**: a single consolidated PR comment is less noisy than 30 inline comments, and easier to archive as a learning signal.

## Related Skills

- `architecture-enforcement` — deterministic checks that should run BEFORE the reviewers
- `edit-lint-feedback-loop` — catches cheap issues before they reach the reviewers

## Gotchas

See `gotchas.md`.

---
> Source: [toru-oizumi/claude-harness-engineering](https://github.com/toru-oizumi/claude-harness-engineering) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
