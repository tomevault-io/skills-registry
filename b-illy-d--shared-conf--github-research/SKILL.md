---
name: github-research
description: Use the gitbug mcp and learn about how code should look Use when this capability is needed.
metadata:
  author: b-illy-d
---

# External Code Research

## Overview

Research external patterns, documentation, and best practices from GitHub and library docs when AI knowledge is insufficient. Use Octocode MCP for GitHub research, Context7 MCP for library documentation, with WebFetch as final fallback.

**Core principle:** Research BEFORE building, not during. External research enhances planning, not execution.

**This skill is ONLY for external research.** Local codebase search is handled by other cc10x tools (Grep/Glob/Read).

## The Iron Law

```
NO EXTERNAL RESEARCH WITHOUT CLEAR AI KNOWLEDGE GAP OR EXPLICIT USER REQUEST
```

If AI training knowledge covers the technology well, skip external research - UNLESS user explicitly asks. This skill is for NEW technologies, complex integrations, unfamiliar APIs, and explicit user requests.

## When to Use

**ALWAYS invoke when:**
- User explicitly requests research ("research X", "how do others", "best practices", "find on github", "use octocode")
- Technology released after 2024 (AI knowledge cutoff)
- Complex integration patterns (auth, payments, real-time)
- Local debugging failed 3+ times with external service errors

**NEVER invoke when:**
- User says "quick" or "simple"
- Standard patterns AI knows well (CRUD, REST, React basics)
- Code review or refactoring tasks
- Technology released before 2024 (unless user explicitly asks)

**The rule:** Trust octocode for HOW. This skill only decides WHEN.

## Availability Check (REQUIRED)

**Before using Octocode tools, verify availability:**

```
# Try a simple package lookup to test MCP availability
mcp__octocode__packageSearch(name="express", ecosystem="npm")
```

**If Octocode unavailable → Fall back to Context7 MCP**
**If Context7 unavailable → Fall back to WebFetch**

## Research Process

### Phase 1: Confirm Need

Before using octocode tools, verify:
1. User explicitly requested research? → Proceed
2. Technology is post-2024? → Proceed
3. Complex integration with uncertainty? → Proceed
4. None of the above? → STOP. Use AI knowledge.

### Phase 2: Let Octocode Guide the Research

**Trust octocode's embedded guidance.** It will:
- Select the right tools and order
- Optimize queries for token efficiency
- Handle pagination and error recovery

**Your job:** Provide clear research goals in the tool parameters:
- `mainResearchGoal`: The overall question you're trying to answer
- `researchGoal`: What this specific query seeks to find
- `reasoning`: Why this query helps answer the main goal

### Phase 3: Summarize for cc10x Memory

Extract only what's needed for the task at hand:
- Key patterns/approaches found
- Gotchas or warnings discovered
- Specific code snippets (minimal)
- Links for future reference

**DO NOT dump entire files - summarize and cite.**

## Fallback Chain

```
TIER 1: Octocode MCP → TIER 2: Context7 MCP → TIER 3: WebFetch
```

Try each tier in order. Fall back only if current tier is unavailable or fails.

**NO LOCAL SEARCH** - This skill is for external research only. Local codebase search uses Grep/Glob/Read directly.

## Output Format

```markdown
## External Research Summary

**Knowledge Gap**: [What we didn't know]

**Research Conducted**:
- Source: [repo/docs URL]
- Query: [what we searched]

**Key Findings**:
1. [Pattern/approach found]
2. [Gotcha or warning]
3. [Relevant code snippet - minimal]

**Application to Task**:
[How this applies to current work]

**References Saved**:
- [URL for future debugging]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/b-illy-d) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
