---
name: consulting-agents
description: Use when you need information you don't have, expertise outside your comfort zone, or a single fresh perspective on code — dispatches individual agents to research, advise, or review. For multi-perspective reviews where agents discuss and converge, use design-meeting instead. NOT for implementation delegation (see subagent-driven-development).
metadata:
  author: snits
---

# Consulting Agents

## The Trigger

**Use this skill when you think:**
- "I need to find something in this codebase"
- "I'm not sure about the best approach here"
- "I want a second opinion on this code/design"
- "I need to research how X works"

**Ask yourself:** "Do I need information, expertise, or a fresh perspective?" If yes → consult an agent.

**NOT for:**
- Implementation work → `subagent-driven-development`
- Multi-perspective review where agents need to discuss and converge → `design-meeting`

## Core Principle

Agents provide **fresh context** for focused tasks. You own the through-line understanding. Agents research, advise, and review.

**No blocking authority.** Agents provide input, you decide.

## When to Consult

| Need | Agent Type | Example |
|------|-----------|---------|
| Find files/components | `codebase-locator` | "Find authentication middleware" |
| Analyze code deeply | `codebase-analyzer` | "How does the caching layer work?" |
| Find similar patterns | `codebase-pattern-finder` | "Find OAuth implementations to reference" |
| Research external docs | `web-search-researcher` | "Best practices for JWT refresh tokens" |
| Domain expertise | `general-purpose` with domain focus | "Review for security issues" |
| Code review | `general-purpose` | "Review this PR for quality" |
| Design validation | `general-purpose` | "Is this architecture sound?" |

## Task Prompt Pattern

**Domain-focused task wording > specialist identity.**

Instead of: "You are a security expert..."
Use: "Review this code for security issues: authentication, authorization, input validation, SQL injection, XSS"

The domain focus triggers expertise without the overconfidence trap.

### Dynamic Role (When Needed)

When consultation needs a clear vantage point, add a role:

```
**Role:** Staff Infrastructure Engineer focused on failure-mode analysis.

**Task:** Review this retry logic for...
```

Keep roles scoped to the consultation. Recompute for each new agent.

## Task Prompt Iteration Protocol

**Before dispatching, refine the prompt:**

1. **Draft task** with what you want and domain concerns
2. **Ask the agent:** "I'm planning to task you with [X]. What additional context would help?"
3. **Agent responds** with needs (usually 1-2 iterations)
4. **Update prompt** with requested info
5. **Confirm:** "Does this have everything needed?"
6. **Dispatch to fresh context** with refined prompt

**Why fresh context:** Same model, clean slate = no accumulated assumptions.

## Parallel Discovery

**Discovery can run in parallel easily** - no commit coordination needed.

Dispatch multiple agents in a SINGLE message when tasks are orthogonal:

```
[In one message, dispatch:]
- codebase-locator: "find authentication entry points"
- codebase-locator: "find session management"
- codebase-locator: "find authorization code"
- general-purpose: "review auth architecture for security concerns"

→ All 4 run concurrently
→ You synthesize results
```

**When to parallelize discovery:**
- Multiple searches needed
- Different review perspectives on same code (security, performance, UX)
- Research from multiple sources
- Any orthogonal read-only operations

**Synthesis required:** You (or a coordinating agent) must synthesize parallel results. Parallel agents catch task-specific issues but miss integration concerns.

## Synthesis Layer

**Problem:** Parallel agents miss how pieces connect.

**Options:**
1. **You synthesize** (default for 2-4 agents)
2. **Coordinating agent** reviews all results (for 5+ agents)
3. **Two-phase:** Parallel task reviews, then integration review

**Decide who synthesizes before parallelizing.**

## Report Format

### Scratchpad Directory (Fallback Chain)

Agents write reports to a **project scratchpad** by default, with a fallback chain:

1. **Project scratchpad** (`${PROJECT_ROOT}/.claude/scratchpad/`) — preferred location
   - If the path exists (directory or symlink), write there
   - If it does not exist, create the project directory in the central repo and symlink it:
     ```bash
     mkdir -p ~/.claude/scratchpad/projects/${PROJECT_SLUG}
     ln -s ~/.claude/scratchpad/projects/${PROJECT_SLUG} ${PROJECT_ROOT}/.claude/scratchpad
     ```
   - `${PROJECT_SLUG}` is the project directory name (e.g., `orbweaver-rs` from `~/devel/orbweaver-rs/`)
2. **Global scratchpad** (`~/.claude/scratchpad/`) — fallback if project scratchpad fails
3. **Project root** (`${PROJECT_ROOT}/`) — last resort if both scratchpads fail
   - **Inform the user** so they can move the report to its proper place
4. If all writes fail, **inform the user** that the report could not be saved

### File Naming

```
{timestamp}-{project-slug}-{agent-type}-{task-slug}.md
```

**Report structure:**
```markdown
# Task: [What you asked]

## Executive Summary
[2-3 sentences: findings + recommendation]

## Findings
[Detailed analysis with evidence]

## Recommendations
[Specific actionable suggestions]

## References
[Files examined, sources consulted]
```

**Objectivity required:** Focus on technical facts, not quality judgments. Avoid superlatives.

## Related Skills

| Skill | Use When |
|-------|----------|
| `design-meeting` | Multiple domain perspectives needed, agents discuss and converge |
| `domain-review-before-implementation` | About to dispatch implementation agent - mandatory review first |
| `subagent-driven-development` | Executing plan tasks sequentially with review gates |
| `parallel-agent-orchestration` | 3+ independent implementation tasks in parallel |

**This skill (consulting-agents):** Single-agent research, expertise, review — agents advise, you decide.

**design-meeting:** Multi-agent team review — agents review, cross-examine, and discuss to converge.

**Other skills:** Implementation delegation — agents write code.

## Decision Matrix

**Consult when:**
- ✅ Need information
- ✅ Want expert opinion
- ✅ Need code review
- ✅ Validating approach
- ✅ Pattern discovery

**Don't consult, implement directly when:**
- ❌ You have the info already
- ❌ Simple/obvious task
- ❌ Need tight context continuity

**Don't consult, delegate implementation when:**
- Task is well-scoped with clear acceptance criteria
- Fresh context beneficial
- See `subagent-driven-development` or `parallel-agent-orchestration`

## Red Flags

**Never:**
- Give agents blocking authority (you decide)
- Skip reading agent reports
- Parallelize without deciding who synthesizes

**Always:**
- Use domain-focused task wording over specialist identity
- Iterate on task prompts before dispatching
- Synthesize parallel results (don't just aggregate)
- Maintain final decision authority

## Why Fresh Context + Domain Focus Works

**Observed (limited sample, Fall 2025 kernel CVE work):** In solo discovery tasks, general-purpose agents with domain-focused prompts outperformed specialist-identity agents. The sample was narrow — kernel backporting specifically — and shouldn't be over-generalized.

**Solo agent risk with strong identity:**
1. **Overconfidence trap** - "You are an expert" → commits to role → skips tool verification
2. **Narrow focus** - Strong identity → gravitates to "hard problems" → misses systematic concerns

**But in teams, this changes.** Individual specialist bias gets cancelled out by teammates with different perspectives — the same way code review catches what a single reviewer misses. A specialist who over-commits to their role gets checked by agents approaching the same problem from other angles.

**Practical guidance:**
- **Solo consultation:** Domain-focused task wording over specialist identity (lower overconfidence risk)
- **Team/multi-agent work:** Specialist identities are fine — team dynamics correct individual bias
- **Always:** Fresh context = clean slate, which helps regardless of identity approach

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/snits) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
