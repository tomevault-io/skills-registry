---
name: readme-wizard
description: >- Use when this capability is needed.
metadata:
  author: debs-obrien
---

# README Wizard

Generate or improve a project's README by scanning the repo and producing a polished, professional result based on real project data.

## Workflow

### 1. Scan the project

Run `scripts/scan_project.sh <project-directory>` to collect structured JSON metadata:
- **Project name** and description
- **License** type
- **Git remote** (owner and repo name)
- **Package manager** (npm, yarn, pnpm, pip, cargo, go, etc.)
- **CI setup** (provider and workflow files)
- **Social links** (YouTube, Discord, Twitter, LinkedIn, Bluesky, Twitch)
- **Directory structure** (top 2 levels)

 The reference script checks local files first, then uses the GitHub API to look up the homepage URL and crawls that homepage for additional social links. Your own first version can stay local-file only and add this enrichment later if you want it.

**Handling missing data:** The scan will return empty strings for anything it can't find. Never fabricate metadata — if a field is empty, skip the related section or badge entirely. For example: no CI workflows means no build badge; no social links means no social section.

### 2. Read the best practices guide

Read `references/readme-best-practices.md` before writing. It covers structure, tone, project-type adaptation, and common pitfalls. This context makes a real difference in output quality — the guide explains the reasoning behind each section so you produce READMEs that feel intentional rather than formulaic.

### 3. Build the README

Use `assets/readme-template.md` as the base structure. Replace `{{PLACEHOLDER}}` markers with actual project data from the scan.

When rendering the project tree, keep it close to how people browse repos: list directories before files, alphabetize within each group, and add trailing `/` markers for directories.

**Adapt — don't copy blindly.** The template is a starting point. Drop sections that don't apply (e.g., no Contributing section for a personal notes repo) and adjust the tone to match the project:
- **Libraries/frameworks**: Focus on installation, API, and examples
- **Applications**: Focus on setup, configuration, and screenshots
- **Docs/learning repos**: Focus on structure and navigation
- **Small utilities/scripts**: Keep it short — hero + what it does + usage is enough

### 4. Add badges

Read `assets/badges.json` for the full badge catalog organized by category (status, social, extras) with `{{PLACEHOLDER}}` markers for dynamic values. Use `style=for-the-badge` for all badges.

**Golden rule:** Only include badges for things that actually exist. A badge for a nonexistent CI workflow or a guessed npm package is worse than no badge at all.

### 5. Validate the output

Review the generated README against the assertions in `evals/evals.json`. Verify:
- No placeholder text remains (`{{PROJECT_NAME}}`, `TODO`, etc.)
- All badges point to real shields.io URLs for things that actually exist
- Sections are proportional to the project — a simple script doesn't need a 200-line README
- Social badges only appear if social links were found in the scan
- The tone is concise and direct, not marketing fluff

### 6. Optionally add a diagram

Only read `assets/diagrams.md` if the project has multiple components or a clear data flow (APIs, monorepos, content pipelines). Skip entirely for simple projects. Generate the diagram from the project's actual structure — the templates in the file are starting points to adapt, not copy.

---
> Source: [debs-obrien/learn-agent-skills](https://github.com/debs-obrien/learn-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
