---
name: codebase-investigation
description: Use when working with a systematic guide for performing forensic audits and heavy-engineering analysis on any software repository. Use this to identify high-value features, architectural patterns, and hidden technical debt.
metadata:
  author: rylezhou
---

# Codebase Investigation Skill

This skill outlines a rigorous, step-by-step methodology for investigating a new codebase. Think of it as a "Forensic Audit" for software. It is designed to quickly reveal the **soul of the system**—what matters, what's broken, and where the engineering team actually spends their time.

## 1. The "Pulse Check" (High-Level Overview)
**Goal:** Understand the project's health, scale, and activity.

*   **List the Vitals:**
    *   `ls -F` (Root structure)
    *   `git log --oneline -n 20` (Recent activity)
    *   `git shortlog -sn --all` (Key contributors)
*   **Identify the Core:**
    *   `find . -maxdepth 2 -not -path '*/.*'` (Where is the code? `src`? `app`? `packages`?)
    *   `cat package.json` (Dependencies = Capabilities. Look for heavy lifters like `bullmq`, `pg`, `react`, `grpc`.)

## 2. The "Heatmap" Analysis (Effort & Churn)
**Goal:** Identify the most "expensive" and "valuable" features.
*   **Where is the work happening?**
    *   `git log --name-only --format="" | sort | uniq -c | sort -rn | head -n 30`
    *   *Interpretation:* High-churn files = Core Logic (or Technical Debt).
*   **What is growing?**
    *   `find . -type f -name "*.ts" -exec wc -l {} + | sort -rn | head -n 20`
    *   *Interpretation:* Large files = God Objects / Monoliths.

## 3. The "Nerve Center" Search (Key Components)
**Goal:** Find the critical pathways.

*   **Routing & Entry Points:**
    *   Search for router definitions: `grep -r "Router" src`, `grep -r "Controller" src`
    *   Search for API entry points: `grep -r "export const GET" src`, `grep -r "@Post" src`
*   **Data Models:**
    *   Search for schemas: `find src -name "*schema*"`, `grep -r "interface" src/types`
*   **Authentication:**
    *   `grep -r "Auth" src`, `grep -r "Middleware" src`
*   **Queues & Jobs (The Backend's "Heart"):**
    *   `grep -r "Queue" src`, `grep -r "Process" src`, `grep -r "Cron" src`

## 4. The "Ghost in the Machine" (Hidden Patterns)
**Goal:** Uncover the unwritten rules and advanced patterns.

*   **Test Density:**
    *   `find src -name "*.test.ts" | wc -l` vs `find src -name "*.ts" | wc -l`
    *   *Interpretation:* Where are the tests? Complex logic has tests. Simple CRUD often doesn't.
*   **Error Handling:**
    *   `grep -r "try" src | wc -l`, `grep -r "catch" src | wc -l`
    *   *Interpretation:* How robust is it?
*   **"TODO" Archeology:**
    *   `grep -r "TODO" src`
    *   *Interpretation:* Where are the bodies buried?

## 5. The "Feature Rank" Synthesis
**Goal:** Produce the final report (like `CLAWDBOT_INSIGHTS.md`).

Combine the data to answer:
1.  **What is the "Crown Jewel"?** (Most edited + Most complex + Most tested)
2.  **What is "Lagacy"?** (Old files, no recent edits, distinct style)
3.  **What is "Bleeding Edge"?** (New folders, active churn, "experimental" flags)

## Example Workflow (The "Arlis" Method)
1.  `run_command` -> `git log --name-only...` -> Result: `src/gateway` is top.
2.  `read_file` -> `src/gateway/server.ts` -> Result: "It handles failovers."
3.  `grep_search` -> "failover" -> Result: 50 matches in `src/gateway`.
4.  **Conclusion:** "Reliability is the #1 feature."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rylezhou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
