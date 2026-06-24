---
name: sync-agent-guidance
description: Adapt and merge reusable repository agent guidance into another project. Use when Codex needs to inspect a target project, create a project-specific AGENTS.md, merge mapped language guidance into existing agent instructions, or update guidance while preserving local rules and project conventions. Use when this capability is needed.
metadata:
  author: luastoned
---

# Sync Agent Guidance

## Overview

Adapt reusable agent guidance from a source directory containing `AGENTS.md`, public mapped language guides, and optional private guides into a target project. This is a synthesis task: read the target project first, then write guidance that fits what is actually there.

## Workflow

1. Identify the source directory. Default to the `guidance/` directory in this code-kit repo when the user does not provide another source.
2. Identify the target project directory from the user request or current working directory.
3. Read source guidance:
   - `AGENTS.md`
   - `Repositories.md` for repository shape, root coordination, ownership boundaries, commit policy, and validation scope
   - Any language guides mapped or referenced by `AGENTS.md` that are relevant to the target project
   - Optional private guides under `guidance/private/` only when the user explicitly asks to include local or private guidance
   - Ignore `guidance/private/.gitkeep`
4. Inspect the target project before editing:
   - Existing root and nested `AGENTS.md` files, plus any language-specific guides they reference
   - Package manifests, lockfiles, workspace files, language/toolchain configs, formatter/linter configs, test configs, build configs, and README/developer docs when present
   - Commit-message tooling such as `.commitlintrc*`, `commitlint.config.*`, package `commitlint` config, and commit hooks
   - Project ownership boundaries, including which folders have their own commands, configs, CI jobs, deployment targets, or release flows
   - Source layout and dominant languages using `rg --files`, excluding generated/vendor directories
5. Decide the target shape:
   - If the target contains multiple projects, preserve or create a concise root `AGENTS.md` for repo-wide rules, and use nested project `AGENTS.md` files for backend/frontend/package-specific implementation rules.
   - If the target is a single-project repository and there is no `AGENTS.md`, create a concise project-specific `AGENTS.md` from the reusable entrypoint rules plus target-specific commands and conventions discovered locally.
   - Include or reference mapped language guidance only when it fits the target project's actual languages and tooling.
   - Supported public guides currently include `C++.md`, `Containers.md`, `Python.md`, `Shell.md`, `TypeScript.md`, and `Repositories.md`; treat `AGENTS.md` as the source of truth if this list changes.
   - If `AGENTS.md` already exists, merge into that file or its referenced language guide. Preserve target-specific rules and add only useful missing guidance.
   - Before editing, inventory every `##` section from each relevant source guide. Treat each section as intentional.
   - The final target guidance must make that inventory auditable: preserve each applicable source `##` section as an explicit target section, or state a concrete omission/move reason in the final response.
   - Do not rely on silently merging multiple source `##` sections into one broad target section. Merging is allowed only when the destination section is obvious and the final response states the source section and destination.
6. Edit manually with `apply_patch`. Review the final diff for duplicated or contradictory rules.

## Merge Rules

- Preserve local rules that mention project architecture, commands, deployment, testing, security, data handling, or ownership.
- Preserve repo-wide rules at the repository root when a target has multiple projects. Do not bury commit style, CI, generated/vendor policy, or project boundary rules inside a single backend/frontend/package guide.
- In multi-project repositories, make the root guidance explain how to identify the owning project, choose nearest commands/configs, group commits by boundary, and validate affected projects.
- Prefer the target project's nearest formatter/linter configs over copied style rules.
- Do not silently drop source sections. Preserve or adapt each relevant source `##` section unless it clearly does not apply, duplicates a stronger local rule, or belongs in a different nested project guide.
- Prefer keeping relevant source `##` sections visible as target headings, adapted to the project. This makes the sync reviewable and prevents accidental omissions.
- If a source `##` section is omitted, moved, or merged into a differently named target section, state the source section, destination or omission, and reason in the final response.
- Do not duplicate sections with the same purpose. If combining with an existing target heading, keep the source section coverage explicit enough to audit.
- Keep language-specific guidance concise. Inline only the parts of mapped language guides that are relevant to the target when a separate language guide is not appropriate.
- If the target already references separate language guides, update the relevant guide instead of inlining a second copy.
- Do not include private overlay guidance in a target project unless the user explicitly asks for it.
- If private guidance is requested, read `guidance/private/AGENTS.md` first when it exists, then load only the private guides it maps or the user names.
- Prefer project-specific commands discovered from manifests, task files, or docs over generic commands.
- Remove source rules that clearly do not fit the target runtime, framework, package manager, or language mix.
- Surface conflicts explicitly in the final response, especially commit format, tooling source of truth, test commands, module system, or stricter typing rules.

## Language Guide Detection

Use the source `AGENTS.md` language mapping as the source of truth for available language guides. Detect the target's relevant guides from files and configuration in the project.

Common signals include:

- File extensions in source files.
- Language-specific config files, compiler configs, build files, and project manifests.
- Package manifests and dependencies that identify the runtime, framework, or test tooling.
- Existing `AGENTS.md` lookup rules or language-specific guides already present in the target.

Do not scan `node_modules`, `dist`, `build`, `.git`, or coverage directories for detection.

## Target Inspection Checklist

Use `rg --files` first. Read only the files needed to understand local conventions.

- Package manager: infer from lockfiles, manifests, and project docs.
- Repository shape: detect whether the target is a single-project or multi-project repository from nested manifests, workspace config, root orchestration files, and nested `AGENTS.md` files.
- Commands: prefer existing scripts for format, lint, typecheck, test, build, and dev.
- Tooling: check nearest formatter, linter, test, build, and compiler configs.
- Commit style: use commitlint/hook config when present; otherwise prefer explicit target docs or source guidance. Treat recent history as a sanity check or fallback only.
- Commit boundaries: inspect changed path groups and preserve project/root boundaries when staging or recommending commits.
- Language configs: read relevant language-specific config files before adding strictness, module, runtime, or compiler guidance.
- Framework/runtime: identify runtimes, frameworks, CLIs, libraries, services, apps, or monorepos.
- Existing docs: keep project-specific workflows from README or developer docs when they affect agent behavior.

## Output Expectations

The final `AGENTS.md` should read as if it was written for the target project, not copied from the source. It should be short enough to follow, specific enough to be useful, and explicit where the target has real commands or constraints.

Include a section-coverage summary in the final response for each relevant source guide: list preserved `##` sections, adapted/renamed sections, moved sections, and omitted sections with reasons.

---
> Source: [luastoned/code-kit](https://github.com/luastoned/code-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
