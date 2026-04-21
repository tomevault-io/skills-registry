---
name: bootstrap-project
description: Bootstrap a project with CI/CD, pre-commit hooks, CLAUDE.md, and framework best practices. Use when setting up a new project, adding CI pipelines, configuring husky/lint-staged, initializing CLAUDE.md, or applying Vercel best practices. Triggers on "bootstrap project", "setup CI", "add pre-commit hooks", "add husky", "setup linting", "add code coverage", "project setup", "init CLAUDE.md", or when starting a new codebase that needs infrastructure. Use when this capability is needed.
metadata:
  author: porkbutts
---

# Project Bootstrap

Ensure a project has CI, pre-commit hooks, CLAUDE.md, and framework best practices configured. Detect what exists, add what's missing.

## Workflow

### 1. Detect Project State

Read `package.json` to determine:
- Package manager (check for `pnpm-lock.yaml`, `yarn.lock`, `bun.lockb`/`bun.lock`, `package-lock.json`)
- Existing scripts (`lint`, `test`, `build`, `typecheck`, `format`)
- Existing devDependencies (`husky`, `lint-staged`, `eslint`, `prettier`, `vitest`, `jest`)
- Framework (`next`, `react`, `vue`, `svelte`)

Check for existing config:
- `.github/workflows/ci.yml` — CI already configured?
- `.husky/` — Husky already initialized?
- Vercel skills already installed?

Report findings to user before making changes.

### 2. GitHub CI Workflow

Ensure `.github/workflows/ci.yml` exists with jobs for **lint**, **typecheck**, **test with coverage**, and **build**.

Read [references/ci-workflow.md](references/ci-workflow.md) for the template and adaptation guide.

If a CI workflow already exists, diff it against requirements and only add missing steps. Do not overwrite existing workflows — merge into them.

Configure coverage thresholds in the test runner config (e.g., `vitest.config.ts`, `jest.config.ts`). LLM coding agents generate tests alongside code, so enforce high thresholds:

| Metric | Threshold |
|--------|-----------|
| Statements | 80% |
| Branches | 80% |
| Functions | 80% |
| Lines | 80% |

CI must **fail** if coverage drops below these thresholds. See [references/ci-workflow.md](references/ci-workflow.md) for configuration examples.

Add a separate `coverage-report` job that posts a coverage summary comment on pull requests. This runs after the main CI job and uses `davelosert/vitest-coverage-report-action` (Vitest) or `MishaKav/jest-coverage-comment` (Jest). See [references/ci-workflow.md](references/ci-workflow.md) for the template and coverage reporter configuration.

### 3. Husky Pre-commit

Ensure husky and lint-staged are installed and configured with checks for **lint**, **format**, and **typecheck**.

Read [references/husky-precommit.md](references/husky-precommit.md) for setup instructions.

Key decisions:
- Include tests in pre-commit only if they run in <30s; otherwise CI-only
- Typecheck runs as a separate hook step (not inside lint-staged)
- lint-staged handles per-file lint + format

### 4. Claude Code Hook Guard

Ensure `.claude/settings.json` has a `PreToolCall` hook that blocks `--no-verify` and `--no-gpg-sign` in git commands. This prevents AI agents from bypassing pre-commit hooks.

Read [references/claude-code-hooks.md](references/claude-code-hooks.md) for the configuration.

If `.claude/settings.json` already exists, merge the hooks into it — do not overwrite existing settings.

Also add a rule to the project's `CLAUDE.md` (create if it doesn't exist) reinforcing the instruction:

```
NEVER use --no-verify or --no-gpg-sign with git commands. Pre-commit hooks must always run. If a hook fails, fix the underlying issue instead of bypassing it.
```

### 5. Initialize CLAUDE.md

Generate a project-level `CLAUDE.md` that gives Claude (and other AI agents) the context they need to work effectively in this codebase. If `CLAUDE.md` already exists, merge new sections into it — do not overwrite existing content.

**Gather context first:**
- Read `package.json` for project name, description, scripts, and dependencies
- Read `README.md` (if it exists) for project purpose and setup instructions
- Read `docs/PRD.md` and `docs/ARCHITECTURE.md` (if they exist) for domain and design context
- Scan the top-level directory structure and key source directories to understand the layout
- Check for frameworks, ORMs, and major libraries in dependencies

**Write the following sections:**

1. **Project overview** — One-paragraph description of what this project is, its purpose, and its primary technology stack.

2. **Architecture** — Brief summary of the architecture (monolith, monorepo, client-server, etc.), key directories, and how they relate. Reference `docs/ARCHITECTURE.md` if it exists rather than duplicating it.

3. **Key commands** — List the common development commands (`dev`, `build`, `test`, `lint`, `typecheck`, `format`) with the correct package manager prefix. Only include commands that actually exist in `package.json` scripts.

4. **Code style and conventions** — Note any conventions visible from the codebase: naming patterns (kebab-case files, PascalCase components), import style (absolute vs relative), test file location (`__tests__/` vs co-located `.test.ts`), and any linter/formatter config.

5. **Rules** — Mandatory rules for AI agents working in this repo. Always include the hook guard rule from step 4. Add any other rules derived from the project's linter config or existing `CLAUDE.md` content.

Keep the file concise — aim for under 80 lines. Prefer short bullet points over prose. Do not include information that's already obvious from file names or standard framework conventions.

### 6. Vercel Best Practices

Install Vercel's agent skills for framework-specific guidance.

Read [references/vercel-best-practices.md](references/vercel-best-practices.md) for details.

Skip if the project doesn't use Vercel or a Vercel-supported framework.

### 7. Verify

After setup, verify everything works:

```bash
# Pre-commit hook fires correctly
git add -A && git commit --dry-run

# CI workflow is valid YAML
cat .github/workflows/ci.yml | python3 -c "import sys, yaml; yaml.safe_load(sys.stdin)"

# Scripts exist and run
<run-command> lint
<run-command> test
<run-command> build
```

Report results to user.

## Missing Tooling

If the project lacks lint/format/test tooling, suggest and install sensible defaults:

| Tool | Default | When |
|------|---------|------|
| Linter | `eslint` | No linter configured |
| Formatter | `prettier` | No formatter configured |
| Test runner | `vitest` | No test runner configured |
| Typecheck | `tsc --noEmit` | TypeScript project, no typecheck script |

Ask the user before installing new tooling — do not assume.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/porkbutts) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
