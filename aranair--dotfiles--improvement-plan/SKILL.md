---
name: improvement-plan
description: Scan an entire repository and produce a long-term improvement plan. Use when the user says "improvement plan", "/improvement-plan", "roadmap", "what should we improve", "long-term plan", "tech debt audit", "codebase review", or asks to analyze the project for future improvements, refactoring opportunities, or strategic technical direction. Use when this capability is needed.
metadata:
  author: aranair
---

# Improvement Plan

Perform a deep, whole-repo analysis and produce a prioritized long-term improvement plan written into the project's notes directory.

## Process

### 1. Discover project structure

- Read `CLAUDE.md` (or equivalent project instructions) for architecture context.
- Run `find` / `ls` to map the full directory tree.
- Identify the primary language, framework, and tooling.

### 2. Scan the codebase

Read **every source file** (not just entrypoints). For large repos, batch reads by module. Focus on:

- **Architecture & modularity** — coupling, cohesion, separation of concerns
- **Error handling & resilience** — missing retries, unhandled promise rejections, crash vectors
- **Type safety** — `any` usage, missing types, loose interfaces
- **Testing** — coverage gaps, missing test infrastructure
- **Configuration & secrets** — hardcoded values, env var validation gaps
- **Performance** — unnecessary allocations, blocking calls, N+1 patterns
- **Observability** — logging gaps, missing metrics, debuggability
- **Security** — input validation, auth checks, data exposure
- **Developer experience** — build speed, linting, CI gaps, documentation
- **Dependencies** — outdated, duplicated, or risky packages
- **Data layer** — schema issues, migration gaps, query patterns
- **Code duplication** — repeated patterns that could be abstracted

### 3. Categorize findings

Group improvements into categories:

- 🏗️ **Architecture**
- 🛡️ **Reliability & Error Handling**
- 🧪 **Testing**
- ⚡ **Performance**
- 🔒 **Security**
- 📊 **Observability**
- 🧹 **Code Quality**
- 🔧 **Developer Experience**
- 📦 **Dependencies**
- 💾 **Data Layer**

### 4. Prioritize

Assign each item:

- **Priority**: P0 (critical) → P3 (nice-to-have)
- **Effort**: S (hours), M (days), L (weeks)
- **Impact**: High / Medium / Low

Sort by priority, then by impact/effort ratio (high impact + low effort first).

### 5. Write the plan

Determine the notes directory:
1. Check for `notes/` in project root (prefer this)
2. Check for `.notes/` in project root
3. If neither exists, create `notes/`

Write the file as `notes/YYYY-MM-DD-improvement-plan.md` using this format:

```markdown
# Long-Term Improvement Plan

**Generated:** YYYY-MM-DD
**Scope:** Full repository scan

## Executive Summary

2-3 sentence overview of the codebase health and top priorities.

## Improvements

### Category Emoji **Category Name**

#### P{n} · {Effort} · {Impact} impact — Title

**What:** Concise description of the improvement.

**Rationale:** Why the codebase needs this — what's currently wrong or missing.

**Impact:** Concrete benefits — what improves, what risks are mitigated, what becomes possible.

**Why it matters:** The deeper reason this is important for the long-term health and success of the project.

---

(repeat for each item)

## Priority Summary

| # | Priority | Title | Effort | Impact |
|---|----------|-------|--------|--------|
| 1 | P0       | ...   | S      | High   |

## Suggested Execution Order

Numbered list grouping items into logical phases or sprints.
```

### 6. Summarize to the user

After writing the file, print:
- Path to the generated file
- Count of improvements found per priority level
- Top 3 highest-priority items as a quick preview

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aranair) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
