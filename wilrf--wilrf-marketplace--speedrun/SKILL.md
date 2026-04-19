---
name: speedrun
description: Use when optimizing codebase performance, reducing bundle sizes, improving build times, or eliminating code bloat. Launches parallel optimization hunters with mandatory before/after verification.
metadata:
  author: wilrf
---

# Speedrun Skill

Comprehensive optimization hunting that dispatches parallel agents to find performance bottlenecks across all layers, then applies fixes with mandatory verification.

## The Iron Law

```
NO OPTIMIZATION CLAIM WITHOUT MEASUREMENT.
```

Every optimization must have before/after metrics. Unverified "improvements" are worthless.

## ROI & Priority Rubric

All hunters use the same priority classification:

| Priority | Impact | Fix Effort |
|----------|--------|------------|
| P0 | >50% improvement or >100KB savings | Trivial (1-2 lines) |
| P1 | 20-50% improvement or 50-100KB savings | Straightforward |
| P2 | 10-20% improvement or 10-50KB savings | Moderate |
| P3 | <10% improvement or <10KB savings | Any |

**Confidence:**
- HIGH: Direct evidence (measured file size, query count, profiler output)
- MEDIUM: Pattern match — savings estimated from code structure
- LOW: Theoretical — no direct measurement possible

Fix order: P0 first. Never spend time on P3 while P0 exists.

## Quick Reference

| Hunter | Profile | Focus | Color |
|--------|---------|-------|-------|
| bundle-hunter | OBSESSIVE | File sizes, tree-shaking, code splitting | yellow |
| complexity-hunter | UNCOMFORTABLE | Cyclomatic complexity, nesting, function length | purple |
| dead-code-hunter | RUTHLESS | Unused exports, orphan files, unreachable paths | gray |
| dependency-hunter | WARY | Bloat, duplicates, outdated, vulnerabilities | magenta |
| build-hunter | IMPATIENT | Compilation time, caching, parallelization | orange |
| algorithm-hunter | IMPATIENT | Big O, data structures, repeated computation | red |
| query-hunter | METICULOUS | N+1, missing indexes, slow queries | green |
| web-vitals-hunter | OBSESSIVE | LCP, CLS, INP, TTFB | blue |
| memory-hunter | PARANOID | Leaks, allocation patterns, GC pressure | red |
| image-hunter | METICULOUS | Formats, compression, lazy loading | cyan |

## Workflow

1. **Measure baseline** - Gather current metrics (bundle size, build time, complexity scores)
2. **Detect stack** - Identify framework, build tooling, database, image handling
3. **Dispatch applicable hunters in PARALLEL** (stack-gated — skip irrelevant hunters)
4. **Collect findings** - Aggregate with P0-P3 priority and confidence ratings
5. **Write SPEEDRUN.md** - Prioritized optimization report
6. **Apply fixes** - P0/P1 safe fixes auto-apply, complex fixes require approval
7. **Verify improvements** - Re-measure and report deltas

## Stack-Gated Dispatch

Always dispatch: bundle, complexity, dead-code, dependency, algorithm

Dispatch if applicable:
- `build-hunter` — has build config (webpack/vite/rollup/turbo)
- `query-hunter` — has database (Supabase/Prisma/SQL/Mongoose)
- `web-vitals-hunter` — has frontend (React/Vue/HTML)
- `memory-hunter` — has server runtime (Node/Python/Go)
- `image-hunter` — has image assets (public/, static/, assets/)

## Fix Tiers

### Auto-Apply (Safe)
- Remove unused imports/exports
- Delete dead code files
- Optimize image formats
- Add missing lazy loading
- Remove unused dependencies

### Require Approval (Risky)
- Algorithm refactors
- Dependency swaps
- Code splitting changes
- Build config modifications
- Database index additions

## Output: SPEEDRUN.md

```markdown
# Speedrun Optimization Report

**Date:** YYYY-MM-DD
**Stack:** [detected tech stack]
**Baseline:** Bundle: X MB | Build: Xs | Complexity avg: Y

## Summary
| Hunter | P0 | P1 | P2 | P3 | Total |
|--------|----|----|----|----|-------|
| bundle-hunter | N | N | N | N | N |
| query-hunter | N | N | N | N | N |
| [etc.] | | | | | |

## P0 — Act Now
[Quick wins: high impact, trivial effort]

### [Issue Title]
- **Priority:** P0
- **Hunter:** bundle-hunter
- **Impact:** ~80KB savings
- **Confidence:** HIGH
- **Fix:** [What to do]

## P1 — High Value
[Straightforward fixes worth doing this sprint]

## P2 — Medium Value
[Moderate effort, good returns]

## P3 — Consider Later
[Minor gains, low urgency]

## Verification
| Metric | Before | After | Delta |
|--------|--------|-------|-------|
| Bundle | X MB | Y MB | -Z% |
| Build | Xs | Ys | -Z% |
| Queries/request | N | M | -Z% |
```

## After Optimization

Re-measure all baseline metrics and report the delta. Refuse to claim an improvement without measurement.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wilrf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
