---
name: code-agent-init
description: Generate fixed AGENTS.md and CLAUDE.md project instruction files from bundled templates. Use when the user asks to create, regenerate, or standardize agent guidance files for Codex, Claude Code, coding assistants, repository agent instructions, AGENTS.md, or CLAUDE.md. Use when this capability is needed.
metadata:
  author: linuxperf
---

# Code Agent Init

## Workflow

1. Identify the target repository root.
   - Use the current working directory when the user does not specify a path.
   - Prefer the nearest directory containing `.git` when working from a subdirectory.

2. Generate the files from this skill's bundled templates:

```bash
skills/code-agent-init/scripts/generate_agent_docs.sh [target-directory]
```

3. Add language-specific AGENTS.md constraints when available.
   - Inspect the target repository to identify the primary implementation language.
   - If `lang/<language>.md` exists for that language, append its contents to the generated `AGENTS.md`.
   - Only add language constraints that match the target repository; do not add every file from `lang/`.

4. Append to existing files by default.
   - If `AGENTS.md` or `CLAUDE.md` already exists, the script appends the matching template content to the existing file.
   - When the user explicitly asks to regenerate, replace, or overwrite the files, run:

```bash
skills/code-agent-init/scripts/generate_agent_docs.sh --force [target-directory]
```

5. Report the generated file paths.
   - Do not edit the generated content unless the user asks for a custom template.
   - Treat `assets/AGENTS.md` and `assets/CLAUDE.md` as the source of truth for the fixed templates.

## Templates

- `assets/AGENTS.md`: fixed Codex-style agent instructions.
- `assets/CLAUDE.md`: fixed Claude Code-style agent instructions.
- `lang/*.md`: optional language-specific constraints appended to `AGENTS.md` when the target repository uses that language.

## Notes

- Keep the templates deterministic and concise.
- If the templates need to change, update the files in `assets/` first, then regenerate target files with `--force`.

---
> Source: [linuxperf/infra-programmer-skills](https://github.com/linuxperf/infra-programmer-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
