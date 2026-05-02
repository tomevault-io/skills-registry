---
name: auditing-bdd-tests
description: Analyzes BDD (Gherkin) + Playwright test solutions for spec quality, flake resistance, semantic/a11y locators, and AI-agent operability. Produces aspect scoring, grade, issues by severity, and improvement roadmap.
metadata:
  author: viktor-silakov
---

# BDD Test Solution Audit

Goal: evaluate **specification executability**, **flake resistance**, **maintainability**, **semantic/a11y quality**, and **AI-agent operability**.

## Adaptive Workflow

Workflow adapts based on repository size (auto-detected).

```
┌─────────────────────────────────────────────────────────────────┐
│ 1. DISCOVER → 2. ANALYZE → 3. SCORE → 4. REPORT → 5. ROADMAP   │
└─────────────────────────────────────────────────────────────────┘
     ↑                                                      │
     └──────────── Skip steps for small repos ──────────────┘
```

| Repo Size | Steps | Sampling | Questions |
|-----------|-------|----------|-----------|
| Small (≤20 scenarios) | 1→3→4 | None | 1 question |
| Medium (21–100) | 1→2→3→4→5 | 30–50% | 2 questions |
| Large (100+) | Full | Stratified | 3 questions |

---

## Step 1: Discovery & Auto-Inference

Target: `{argument OR cwd}`

**Auto-detect (no user input needed):**

| What | How to Detect |
|------|---------------|
| Stack | `playwright.config.*` → Playwright; `playwright-bdd` in package.json → playwright-bdd |
| Size | Count `*.feature` files and `Scenario:` lines |
| History | Check `.bddready/history/index.json` exists |
| CI | Check `.github/workflows/`, `Jenkinsfile`, `.gitlab-ci.yml` |
| Artifacts | Check `playwright.config.*` for trace/video/screenshot settings |

**Output immediately:**
```
Target: {path}
Stack: {stack} (auto-detected)
Size: {small/medium/large} ({N} features, {M} scenarios)
History: {yes/no} | CI: {yes/no} | Artifacts: {configured/missing}
```

See `modules/discovery.md` for detailed detection rules.

---

## Step 2: Sampling (Medium/Large repos only)

Skip for small repos — analyze all scenarios.

For medium/large repos, use stratified sampling. See `modules/sampling.md`.

---

## Progress Indicator (Medium/Large repos)

For repositories with 50+ scenarios, show progress during analysis:

```
Analyzing BDD Test Solution...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 0%

[■■■■■■■■■■░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░] 25%
✓ Discovery complete (playwright-bdd detected)
→ Analyzing features/auth/*.feature (8 scenarios)

[■■■■■■■■■■■■■■■■■■■■░░░░░░░░░░░░░░░░░░░░] 50%
→ Analyzing features/checkout/*.feature (12 scenarios)

[■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■░░░░░░░░░░] 75%
→ Scoring aspects...

[■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■] 100%
✓ Analysis complete
```

**Progress stages:**
1. Discovery (10%)
2. Feature file analysis (10-70%, proportional to file count)
3. Step definition analysis (70-85%)
4. Scoring (85-95%)
5. Report generation (95-100%)

Update progress after each feature file or major step.

---

## Step 3: Score Aspects

Score each aspect using rubrics from `criteria/aspects.md`.

**Aspects and weights:**

| # | Aspect | Weight |
|---|--------|--------|
| 1 | Executable Gherkin | 16% |
| 2 | Step Definitions Quality | 14% |
| 3 | Test Architecture | 14% |
| 4 | Selector Strategy | 12% |
| 5 | Waiting & Flake Resistance | 14% |
| 6 | Data & Environment | 10% |
| 7 | CI, Reporting & Artifacts | 10% |
| 8 | AI-Agent Operability | 10% |

**Scoring:** 0 (bad) / 5 (partial) / 10 (good) per criterion.

See `modules/scoring.md` for calculation formulas.

---

## Step 4: Report

### 4.1 Terminal Output (Always)

Print ASCII dashboard with scores and issues. See `modules/output-formats.md`.

### 4.2 Issues by Severity

Classify using `reference/severity.md`:
- 🔴 **CRITICAL** — blocks reliable execution
- 🟡 **WARNING** — hinders speed/maintainability  
- 🔵 **INFO** — optimizations

**Every issue MUST have:**
- Evidence (file path, pattern, or code snippet)
- Impact (why it matters)
- Effort estimate (Low/Medium/High)

### 4.3 Save Reports

Save to `.bddready/history/reports/`:
- `{REPORT_ID}.json` — machine-readable
- `{REPORT_ID}.md` — human-readable

Update `.bddready/history/index.json` for delta tracking.

### 4.4 HTML Report (Offer to User)

After showing terminal output, ask:

> Would you like me to generate an interactive HTML report?

If yes, run:
```bash
node scripts/render-html.mjs .bddready/history/reports/{REPORT_ID}.json .bddready/history/reports/{REPORT_ID}.html
```

---

## Interactive Fix Mode

After showing issues, offer to fix quick wins immediately.

### Trigger Conditions

Offer interactive fixes when:
- At least 1 CRITICAL issue with `Effort: Low`
- Issue has clear, automatable fix pattern

### Flow

```
╔══════════════════════════════════════════════════════════════════╗
║                     QUICK FIX AVAILABLE                          ║
╠══════════════════════════════════════════════════════════════════╣
║  [C1] Flake Resistance: Found 7 arbitrary sleeps                 ║
║       Fix: Replace `wait X seconds` with condition waits         ║
║       Effort: Low | Files: 3                                     ║
║                                                                  ║
║  → Fix C1 now? [y/n/skip all]                                    ║
╚══════════════════════════════════════════════════════════════════╝
```

### Response Handling

| Response | Action |
|----------|--------|
| `y` / `yes` | Apply fix, show diff, continue to next fixable issue |
| `n` / `no` | Skip this issue, continue to next |
| `skip all` / `s` | Skip interactive mode, show full report |

### Fixable Patterns

| Issue Pattern | Auto-Fix |
|---------------|----------|
| `wait X seconds` without condition | → `waitFor` with visibility/enabled check |
| Hardcoded `sleep()` | → `waitForSelector()` or `waitForResponse()` |
| CSS class selectors | → `getByRole()` / `getByTestId()` (suggest, confirm) |
| Missing `trace: 'on-first-retry'` | → Add to playwright.config |
| Duplicate step definitions | → Consolidate (show which to keep) |

### After Each Fix

```
✓ Fixed C1: Replaced 7 sleeps with condition waits
  Modified: features/checkout.feature, features/auth.feature
  
→ Fix C2 now? [y/n/skip all]
```

### Post-Fix Summary

```
╔══════════════════════════════════════════════════════════════════╗
║                     FIX SUMMARY                                  ║
╠══════════════════════════════════════════════════════════════════╣
║  ✓ C1: Fixed (7 sleeps → condition waits)                        ║
║  ✓ C3: Fixed (added trace-on-failure)                            ║
║  ✗ C2: Skipped (requires manual review)                          ║
║                                                                  ║
║  Files modified: 5                                               ║
║  New score estimate: 68 → 74 (+6)                                ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Step 5: Roadmap (Medium/Large repos only)

Skip for small repos — provide inline recommendations instead.

| Phase | Focus |
|-------|-------|
| 1: Quick Wins | Remove sleeps, enable trace-on-failure, fix critical selectors |
| 2: Foundation | Thin step defs, proper fixtures, test isolation |
| 3: Advanced | Visual tests, a11y integration, CI optimization |

---

## User Questions

### Auto-Inference First

Before asking, try to infer from codebase:

| Question | Auto-Inference |
|----------|----------------|
| Primary goal? | Infer from issues: many sleeps → stability; bad selectors → AI-ready |
| Depth of changes? | Infer from repo size: small → quick wins; large → phased |
| CI constraints? | Read from config: worker count, timeout settings |

### Minimal Question Set

Ask ONLY what cannot be inferred:

**Small repos (1 question):**
> What is your priority: stability, speed, or AI-agent readability?

**Medium repos (2 questions):**
1. What is your priority: stability, speed, or AI-agent readability?
2. How deep can changes go: quick fixes only, or can we refactor?

**Large repos (3 questions):**
1. What is your priority: stability, speed, or AI-agent readability?
2. How deep can changes go: quick fixes only, medium refactor, or deep restructuring?
3. Are there CI/environment constraints? (e.g., worker limits, no mocks, staging only)

### Dynamic Questions (Only if triggered)

Ask ONLY if specific issues found:

| Trigger | Question |
|---------|----------|
| CRITICAL issues found | Which CRITICAL items should be fixed first? (list by ID) |
| Selector/a11y issues | Can we modify application markup (HTML), or tests only? |
| >10 WARNING issues | Which WARNING items are in scope this iteration? |

---

## Semantic/A11y Refactoring Proposal

If Aspect 4 (Selector Strategy) or Aspect 8 (AI-Agent Operability) scores below 60, propose:

```
╔══════════════════════════════════════════════════════════════════╗
║           SEMANTIC/A11Y REFACTORING PROPOSAL                     ║
╠══════════════════════════════════════════════════════════════════╣
║  Your locators would be more stable with semantic HTML.          ║
║                                                                  ║
║  Would you like me to help refactor:                             ║
║  [ ] Component markup (replace div onclick → button, add ARIA)   ║
║  [ ] Test locators (migrate CSS → getByRole)                     ║
╚══════════════════════════════════════════════════════════════════╝
```

Ask only if user has access to modify application source code.

---

## Reference Files

| File | Purpose |
|------|---------|
| `criteria/aspects.md` | Detailed scoring rubrics (0/5/10) |
| `reference/severity.md` | Issue classification rules |
| `reference/bdd-best-practices.md` | Best practices guide |
| `modules/discovery.md` | Discovery details |
| `modules/sampling.md` | Sampling strategy |
| `modules/scoring.md` | Score calculation |
| `modules/output-formats.md` | Output format specs |
| `templates/report.html` | HTML report template |
| `scripts/render-html.mjs` | HTML generator script |

---

## Quick Reference: Workflow by Size

### Small Repo (≤20 scenarios)
1. Discover (auto-detect stack, size)
2. Ask 1 question (priority)
3. Analyze all scenarios
4. Score aspects (simplified)
5. Print terminal report + issues
6. **Interactive fix mode** (if Low-effort CRITICAL issues)
7. Offer HTML report
8. Provide inline recommendations

### Medium Repo (21–100 scenarios)
1. Discover (auto-detect)
2. Ask 2 questions
3. Sample 30–50%
4. **Show progress** (50+ scenarios)
5. Full aspect scoring
6. Terminal + saved reports
7. **Interactive fix mode**
8. Offer HTML report
9. Phased roadmap (3 phases)

### Large Repo (100+ scenarios)
1. Discover (auto-detect)
2. Ask 3 questions
3. Stratified sampling
4. **Show progress** (with stage updates)
5. Full aspect scoring
6. All report formats
7. **Interactive fix mode**
8. HTML report
9. Detailed phased roadmap
10. Propose a11y refactoring if applicable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/viktor-silakov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
