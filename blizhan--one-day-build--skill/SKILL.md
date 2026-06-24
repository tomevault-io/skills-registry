---
name: one-day-build
description: Production-ready project foundation in 5-8 hours. Architecture-driven human-AI pair programming workflow for bootstrapping greenfield projects with clean architecture, comprehensive tests, and documentation. Use when (1) starting new projects from scratch, (2) user says "let's build X from scratch", "help me architect", "pair program with me", (3) need structured foundations. NOT for quick fixes, single-file changes, or existing codebase modifications. Use when this capability is needed.
metadata:
  author: blizhan
---

# One Day Build

Production-ready project foundation in a single session (5-8 hours).

**Philosophy:** Architecture first → Document decisions → Refactor ruthlessly → Test what differs

## Quick Reference

| Phase | Duration | Focus | Exit Criteria |
|-------|----------|-------|---------------|
| 1. Intent | 30 min | What & Why | 3-5 user stories documented |
| 2. Architecture | 60-90 min | How | AGENTS.md with decisions |
| 3. Foundation | 2-3 hrs | Build | 1 feature working, >80% coverage |
| 4. Refinement | 1-2 hrs | Fix | Bugs fixed, code cleaned |
| 5. Testing | 60 min | Verify | Test factories, fast suite |
| 6. Docs | 30 min | Record | README + AGENTS.md current |

## Phase Workflow

### 1. Intent Clarification

Ask clarifying questions until requirements are concrete:
- Vision, constraints, tech preferences?
- Must-have vs nice-to-have?
- Sample data available?

```
User: "I want to build a stock price tracker"
→ "Which APIs? Real-time or daily? CLI, web, or both? Alerts needed?"
→ Result: Yahoo Finance API, daily updates, CLI + web dashboard, email alerts
```

**Checklist:** [ ] 3-5 user stories [ ] Constraints doc [ ] Priority list

### 2. Architecture Design

Propose 2-3 approaches with trade-offs. Document in AGENTS.md.

**Key decisions:**
- Patterns: Template Method (workflows), Strategy (algorithms), Factory (creation)
- Extensibility: How to add new data sources/formats?
- Config: What's configurable vs hardcoded?

**Checklist:** [ ] AGENTS.md created [ ] Interfaces defined [ ] Config schema

### 3. Foundation Implementation

1. **Setup** (15 min) - structure, deps, git
2. **Core** (45 min) - base classes, error handling
3. **First feature** (60 min) - complete with real data
4. **Tests** (30 min) - fixtures, >80% coverage

**Checklist:** [ ] 1 feature works [ ] Tests pass [ ] Makefile ready

### 4. Problem-Driven Refinement

**Cycle:** Discover → Analyze → Design → Implement → Validate

Only refactor when problems emerge. Don't pre-optimize.

```
Bug: Year 1948 showing as 2025
→ Root cause: No year validation
→ Fix: Add _is_valid_year() + test
```

**Checklist:** [ ] Bug fixed [ ] Regression test added [ ] Docs updated

### 5. Testing Architecture

Goal: Make testing easier than not testing.

```python
# Factory pattern - one line = full test suite
TestStockFetcher = create_fetcher_tests(StockFetcher, sample_data, expected)
```

**Checklist:** [ ] Test utilities exist [ ] <5s run time [ ] >80% coverage

### 6. Documentation Sync

Maintain: `AGENTS.md` (decisions), `TODO.md` (backlog), `README.md` (usage)

**Principle:** If not documented, it doesn't exist.

**Checklist:** [ ] Examples work [ ] Decisions recorded [ ] Next steps clear

## Roles

| Agent | Human |
|-------|-------|
| Propose options with trade-offs | Choose direction |
| Implement and test | Provide domain knowledge |
| Document decisions | Validate against real use |
| Challenge assumptions | Accept/reject trade-offs |

## Variations

| Scenario | Use Phases |
|----------|------------|
| Greenfield | 1-6 (full) |
| Legacy refactor | 3-5 |
| Feature addition | 2-5 |
| Bug hunt | 4-5 |

## Anti-Patterns

| Problem | Fix |
|---------|-----|
| Analysis paralysis | Time-box design to 90 min |
| Premature optimization | Solve today's problems only |
| Doc debt | Document decisions immediately |
| Scope creep | Add to TODO.md, stay focused |

For detailed role breakdowns and examples, see [references/phases.md](references/phases.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/blizhan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
