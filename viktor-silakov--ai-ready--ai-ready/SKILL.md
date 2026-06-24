---
name: ai-ready
description: Analyzes repositories for AI agent development efficiency. Scores 8 aspects (documentation, architecture, testing, type safety, agent instructions, file structure, context optimization, security) with ASCII dashboards. Use when evaluating AI-readiness, preparing codebases for Claude Code, or improving repository structure for AI-assisted development.
metadata:
  author: viktor-silakov
---

# AI-Readiness Analysis

Evaluate repository readiness for AI-assisted development across 8 weighted aspects.

## Workflow Checklist

Copy and track progress:

```
AI-Readiness Analysis Progress:
- [ ] Step 1: Discover repository
- [ ] Step 2: Gather user context (Q1-Q4)
- [ ] Step 3: Analyze 8 aspects
- [ ] Step 4: Calculate scores and grade
- [ ] Step 5: Display ASCII dashboard
- [ ] Step 6: Present issues by severity
- [ ] Step 7: Priority survey (Q5-Q9)
- [ ] Step 8: Enter plan mode
- [ ] Step 9: Create phased roadmap
- [ ] Step 10: Generate templates
- [ ] Step 11: Save reports to .aiready/ (confirm HTML generation)
- [ ] Step 12: Ask to open HTML report
```

---

## Step 1: Repository Discovery

```
Target: {argument OR cwd}
```

Discover:
1. **Language/Framework:** Check package.json, Cargo.toml, go.mod, pyproject.toml
2. **History:** Check `.aiready/history/index.json` for delta tracking
3. **Agent files:** CLAUDE.md, AGENTS.md, .cursorrules, copilot-instructions.md

---

## Step 2: Context Gathering

Use AskUserQuestion with these 4 questions:

| Q | Question | Options |
|---|----------|---------|
| Q1 | Rework depth? | Quick Wins / Medium / Deep Refactor |
| Q2 | Timeline? | Urgent / Planned / Strategic / Continuous |
| Q3 | Team size? | Solo / Small (2-5) / Large (5+) / Open Source |
| Q4 | AI tools used? | Claude Code / Copilot / Cursor / Windsurf / Aider (multiselect) |

Store responses for Steps 6 and 11.

---

## Step 3: Analyze 8 Aspects

Evaluate each criterion 0-5-10. See **[criteria/aspects.md](criteria/aspects.md)** for full rubrics.

| Aspect | Weight | Criteria |
|--------|--------|----------|
| Documentation | 15% | 19 |
| Architecture | 15% | 18 |
| Testing | 12% | 23 |
| Type Safety | 12% | 10 |
| Agent Instructions | 15% | 25 |
| File Structure | 10% | 13 |
| Context Optimization | 11% | 20 |
| Security | 10% | 12 |

---

## Step 4: Calculate Scores

```
Aspect Score = (Sum of criteria / Max points) × 100

Overall = (Doc × 0.15) + (Arch × 0.15) + (Test × 0.12) + (Type × 0.12)
        + (Agent × 0.15) + (File × 0.10) + (Context × 0.11) + (Security × 0.10)
```

| Grade | Range |
|-------|-------|
| A | 90-100 |
| B | 75-89 |
| C | 60-74 |
| D | 45-59 |
| F | 0-44 |

---

## Step 5: Display Dashboard

```
╔══════════════════════════════════════════════════════════════════════════════╗
║                          AI-READINESS REPORT                                  ║
║  Repository: {name} | Language: {lang} | Framework: {fw}                     ║
╠══════════════════════════════════════════════════════════════════════════════╣
║  OVERALL GRADE: {X}     SCORE: {XX}/100     {delta}                          ║
╠══════════════════════════════════════════════════════════════════════════════╣
║  1. Documentation       {bar} {score}/100 {delta}                            ║
║  2. Architecture        {bar} {score}/100 {delta}                            ║
║  3. Testing             {bar} {score}/100 {delta}                            ║
║  4. Type Safety         {bar} {score}/100 {delta}                            ║
║  5. Agent Instructions  {bar} {score}/100 {delta}                            ║
║  6. File Structure      {bar} {score}/100 {delta}                            ║
║  7. Context Optimization{bar} {score}/100 {delta}                            ║
║  8. Security            {bar} {score}/100 {delta}                            ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

**Progress bars:** `████████░░` = 80/100 (█ filled, ░ empty, 10 chars total)

**Deltas:** `↑+N` improvement | `↓-N` decline | `→0` unchanged | `(new)` first run

**Issue Summary Block:**
```
╔══════════════════════════════════════════════════════════════════════════════╗
║                          ISSUE SUMMARY                                        ║
╠══════════════════════════════════════════════════════════════════════════════╣
║   🔴 CRITICAL     {bar}  {N}                                                 ║
║   🟡 WARNING      {bar}  {N}                                                 ║
║   🔵 INFO         {bar}  {N}                                                 ║
║   Distribution by Aspect: (sorted by issue count)                            ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

If history exists, show **Progress Over Time** chart with trend analysis.

---

## Step 6: Present Issues

Group by severity, then aspect. See **[reference/severity.md](reference/severity.md)** for classification.

```
🔴 CRITICAL ({N})
──────────────────────────────────────────────────────────────────────
[C1] {Aspect}: {Issue}
     Impact: {description}
     Effort: Low/Medium/High

🟡 WARNING ({N})
──────────────────────────────────────────────────────────────────────
[W1] {Aspect}: {Issue}
     Impact: {description}
```

---

## Step 7: Priority Survey

Use AskUserQuestion for prioritization:

| Q | Question | Purpose |
|---|----------|---------|
| Q5 | Priority areas (top 3)? | Focus recommendations |
| Q6 | Critical issue order? | Prioritize fixes |
| Q7 | Which warnings to fix? | Scope work |
| Q8 | Constraints? | Legacy code, compliance, CI/CD |
| Q9 | Success metrics? | Target grade, zero critical |

**Filter by rework depth from Q1:**
- Quick Wins → Phase 1 only
- Medium → Phases 1-2
- Deep → All phases

---

## Step 8: Enter Plan Mode

After survey, use EnterPlanMode tool.

---

## Step 9: Phased Roadmap

| Phase | Focus | Examples |
|-------|-------|----------|
| 1: Quick Wins | File creation, config | CLAUDE.md, .aiignore, llms.txt |
| 2: Foundation | Structural changes | ARCHITECTURE.md, file splitting, types |
| 3: Advanced | Deep improvements | Coverage >80%, ADRs, architecture enforcement |

---

## Step 10: Generate Templates

For selected issues, generate from templates:

- **CLAUDE.md:** See **[templates/CLAUDE.md.template](templates/CLAUDE.md.template)**
- **ARCHITECTURE.md:** See **[templates/ARCHITECTURE.md.template](templates/ARCHITECTURE.md.template)**

---

## Step 11: Save Reports

Before writing the HTML file, always ask the user:
```
AskUserQuestion:
  Question: "Generate HTML report now?"
  Options: ["Yes, generate HTML", "No, skip HTML"]
```
If "Yes", create the HTML report. If "No", skip HTML but still write Markdown/JSON.

Save to `.aiready/history/reports/` with timestamp:

```
.aiready/
├── config.json              # User preferences
├── history/
│   ├── index.json           # Report index for delta tracking
│   └── reports/
│       ├── {YYYY-MM-DD}_{HHMMSS}.md
│       ├── {YYYY-MM-DD}_{HHMMSS}.html
│       └── {YYYY-MM-DD}_{HHMMSS}.json
```

**Markdown report:** Scores, issues, recommendations, user context
**HTML dashboard:** See **[templates/report.html](templates/report.html)**
**JSON data:** Raw scores for delta tracking

Update `index.json` with new report entry and trend analysis.

### Open Report

If the HTML report was generated and saved, immediately ask:

```
AskUserQuestion:
  Question: "Open HTML report in browser?"
  Options: ["Yes, open report", "No, skip"]
```

If HTML was skipped, do not prompt to open. If yes, run:
```bash
open .aiready/history/reports/{timestamp}.html
```

---

## Validation Loop

After each major step, verify:

1. **After analysis:** All 8 aspects scored?
2. **After issues:** Severity correctly classified?
3. **After survey:** User selections captured?
4. **After templates:** Files properly generated?
5. **After save:** Reports written to .aiready/?

If validation fails, return to the failed step.

---

## Quick Reference

| File | Content |
|------|---------|
| [criteria/aspects.md](criteria/aspects.md) | Full scoring rubrics for all 8 aspects |
| [reference/severity.md](reference/severity.md) | Issue severity classification |
| [templates/CLAUDE.md.template](templates/CLAUDE.md.template) | Agent instructions template |
| [templates/ARCHITECTURE.md.template](templates/ARCHITECTURE.md.template) | Architecture doc template |
| [templates/report.html](templates/report.html) | HTML dashboard template |
| [examples/](examples/) | Example reports |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/viktor-silakov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
