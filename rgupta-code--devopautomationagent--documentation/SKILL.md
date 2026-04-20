---
name: documentation
description: Standards and templates for technical, architectural, and operational documentation in the Antigravity ecosystem. Use when this capability is needed.
metadata:
  author: rgupta-code
---

# Documentation Skill

This skill ensures that all project documentation is consistent, professional, and useful for both developers and stakeholders.

## Core Documentation Types

### 1. Technical Walkthroughs (`Walkthrough.md`)
- **Objective**: Explain *how* and *why* changes were made.
- **Requirements**:
  - Link to specific commits or PRs.
  - Describe architectural decisions (e.g., "Refactored to Factory pattern").
  - List files modified and their impact.
  - Document any breaking changes.

### 2. Operational Guides (`SETUP.md`, `QUICKSTART.md`)
- **Objective**: Enable a new developer to run the project in < 5 minutes.
- **Requirements**:
  - Prerequisites (SDK versions, Database tools).
  - Step-by-step installation commands.
  - Troubleshooting common environment issues.

### 3. API & Service Docs
- **Objective**: Document public interfaces and data models.
- **Requirements**:
  - Clear descriptions for every public member.
  - JSON examples for data models.
  - Usage examples for complex services.

### 4. Code Guardian Reports
- **Objective**: Daily snapshot of project health.
- **Requirements**:
  - Summary of test results.
  - Snapshot of current active branch/commit.
  - Highlighted regressions or newly covered areas.

## Style Guidelines

- **Markdown Excellence**: Use H1 for titles, H2 for sections. Use code blocks for all terminal commands and code snippets.
- **Visual Aids**: Use tables for data and lists for steps.
- **Tone**: Professional, clear, and proactive.
- **Zero Placeholder Policy**: Never leave "Coming soon" or "TBD" blocks. Use the `documentation` skill to fill them with actual context.

## Usage Instructions

When generating or updating documentation:
1. Identify the target audience (Developer vs. System Reviewer).
2. Select the appropriate template from `resources/templates.md`.
3. Populate the documentation with real data from the current workspace.
4. Verify all links and paths are correct.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rgupta-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
