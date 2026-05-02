---
name: agentic-revcat
description: Safe-first playbook for using the revcat CLI in agentic development tasks (bugfix, refactor, PR prep), with strict mutation guardrails and reliable troubleshooting flows. Use when this capability is needed.
metadata:
  author: izantech
---

# Agentic Revcat

## When to Use This Skill

Use this skill whenever an agent needs to run RevenueCat API operations through `revcat`, especially for:

- Bugfix tasks that require inspecting or reproducing API-level behavior
- Refactors that must verify command compatibility and output stability
- PR prep tasks that need reproducible command evidence and safe mutation handling
- Diagnosis of failed `revcat` runs
- Planning or executing mutating operations (`POST`, `DELETE`)

Trigger phrases include:

- "run RevenueCat operation"
- "diagnose this revcat error"
- "list available RevenueCat commands"
- "create/delete/update via revcat"

## Safety Contract (Non-Negotiable)

1. Treat `POST` and `DELETE` operations as mutating.
2. Never run mutating operations without both flags:
   - `--apply`
   - `--yes`
3. Prefer a read-only command path before mutation:
   - discover operation
   - inspect parameters
   - validate payload/paths
4. Use `--format json` when command output must be consumed by automation or compared in CI.

## Execution Workflow

1. Pre-flight
   - `npx revcat --help`
   - `npx revcat doctor`
2. Discover
   - `npx revcat api operations`
   - optional tag filter: `--tag <TagName>`
3. Inspect
   - `npx revcat api show <operation-id>`
4. Execute
   - read-only first (`GET`)
   - mutation only with `--apply --yes`
5. Validate
   - confirm command exit behavior and returned data
   - prefer JSON output for deterministic checks

## Task Playbooks

### Bugfix Playbook

1. Capture failing command and exact error output.
2. Run `doctor` to verify credentials/project context.
3. Discover and inspect target operation (`api operations`, `api show`).
4. Re-run with explicit `--path`/`--query`/`--body-*` values.
5. If mutating, enforce `--apply --yes`.
6. Record working command in PR notes.

### Refactor Playbook

1. Identify command/operation IDs affected by the refactor.
2. Execute representative read-only commands in JSON mode.
3. Compare key output fields before/after changes.
4. For any mutation smoke test, use explicit fixture payload plus `--apply --yes`.
5. Document compatibility notes for reviewers.

### PR Prep Playbook

1. Add 2-5 concrete command examples used for verification.
2. Include one "safe mutation" example when write paths changed.
3. Include one error-recovery example if behavior changed.
4. Ensure examples are script-friendly (`--format json` where relevant).

## Tool Routing

Use this routing when deciding which agentic tool should drive the flow:

- Codex CLI: best default for repo-aware edits, command execution, and deterministic validation.
- Claude Code: strong for drafting plans/explanations before command execution.
- Cursor: useful when working directly in Cursor projects that consume `.cursor/rules` guidance automatically.
- Aider: useful for fast iterative code edits when command flow is already known.
- Gemini CLI: useful for quick command brainstorming; validate final commands with `revcat` help and operation metadata.

## References

Load these references as needed:

- Command patterns and safe invocation:
  - `references/command-templates.md`
- Error diagnosis and corrective actions:
  - `references/error-recovery.md`
- Prompt adapters for each supported agentic tool:
  - `references/tool-adapters.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/izantech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
