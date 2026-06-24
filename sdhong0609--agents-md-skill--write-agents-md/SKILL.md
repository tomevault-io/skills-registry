---
name: write-agents-md
description: Use when Codex needs to create, rewrite, audit, slim down, or install AGENTS.md/CLAUDE.md agent instruction files for a repository, especially when avoiding /init-style generated context, reducing context debt, adding repo-specific pitfalls, or setting nested AGENTS.md guidance.
metadata:
  author: sdhong0609
---

# Write AGENTS.md

## Core Principle

Treat `AGENTS.md` as a compact map of project-specific traps and verification rules, not as a README for agents.

Only include information that changes agent behavior and is hard to infer from the repo itself.

## Workflow

1. Identify the target scope.
   - Root repo? Monorepo package? Existing `CLAUDE.md` mirror?
   - If scope is unclear, ask before editing.

2. Read current evidence.
   - Existing `AGENTS.md`, `CLAUDE.md`, `README.md`
   - Package scripts, test config, CI config, lint/typecheck commands
   - Nearby files only as needed to verify non-obvious conventions

3. Filter every candidate line.
   - Keep: exact commands, required flags, forbidden substitutions, repeated agent mistakes, deployment/runtime traps, security/performance/UX guardrails, PR/commit checks.
   - Remove: folder tours, stack lists, README duplicates, broad architecture essays, obvious style rules, team background.

4. Prefer nested files for large workspaces.
   - Root `AGENTS.md`: routing and global rules only.
   - Subdirectory `AGENTS.md`: package-specific traps and commands.
   - The closest file should carry the most specific instruction.

5. Write in Korean by default unless the repo clearly uses English docs.
   - Use bullets and short sections.
   - Keep the file small. Aim for under 80 lines unless the repo genuinely needs more.
   - Each changed line must trace to discovered repo evidence or user-provided constraints.

6. Verify.
   - Re-read the final file.
   - Check that commands are executable-looking and not invented.
   - Mention any assumptions or missing commands instead of guessing.

## Starter Install Command

To install a lean starter `AGENTS.md` in the current project:

```bash
agents-md-init
```

Useful variants:

```bash
agents-md-init /path/to/project
agents-md-init --print
agents-md-init --force
```

The command writes a cautious starter template only. After installing it, inspect the project and replace placeholders with evidence-backed rules.

## Skill Install Command

To install this skill itself:

```bash
agents-md-skill install
```

Useful variants:

```bash
agents-md-skill install --scope user
agents-md-skill install --scope project
agents-md-skill install --agent claude --scope user
agents-md-skill install --agent gemini --scope project
agents-md-skill install --agent all --scope user
agents-md-skill install /path/to/project --agent codex --scope project
```

Default behavior is `--scope user --agent codex`. Use `--force` to overwrite an existing installed copy.

## Reference

Read `references/principles.md` when you need the inclusion/exclusion checklist, anti-patterns, or examples.

---
> Source: [sdhong0609/agents-md-skill](https://github.com/sdhong0609/agents-md-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
