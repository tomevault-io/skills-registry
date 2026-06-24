---
name: cross-agent-export
description: Use when exporting CoderSteroids instructions to other agent harnesses, creating AGENTS.md or CLAUDE.md files, checking cross-agent portability, or preparing a repository for Codex, Claude, Cursor, Gemini, OpenCode, or similar coding agents
metadata:
  author: fabiocantone
---

# Cross Agent Export

## Overview

CoderSteroids is Codex-first, but project instructions should be portable. This skill creates lightweight exports that preserve the core methodology without pretending every host supports Codex skills.

## Export Gate

Before exporting:

1. Identify the target repository.
2. Check whether `AGENTS.md`, `CLAUDE.md`, `.cursor/rules`, or other agent files already exist.
3. Preserve existing user/repo instructions.
4. Prefer additive generated files unless the user approves overwriting.

## Required Export Content

Cross-agent exports must include:

- instruction priority;
- roadmap/wiki continuity;
- current-doc and known-issue research;
- planning/spec discovery;
- test-first and review rules;
- branch/workspace lifecycle;
- observability/logging for runtime diagnosis;
- verification before completion;
- post-task memory flush.

## Script

Use `scripts/cross-agent-export.sh` when available:

```bash
./scripts/cross-agent-export.sh /path/to/repo
./scripts/cross-agent-export.sh --check /path/to/repo
./scripts/cross-agent-export.sh --force /path/to/repo
```

## Completion Criteria

- Existing instructions are not overwritten silently.
- Exported files explain that CoderSteroids is the source methodology.
- `--check` passes after export.
- Roadmap/wiki record durable cross-agent setup decisions when relevant.

---
> Source: [fabiocantone/codersteroids](https://github.com/fabiocantone/codersteroids) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
