---
name: bootstrap-project
description: One-command Codex project setup orchestrator. Detects the stack, then runs root AGENTS.md setup, scoped AGENTS.md placement, Codex permission/config review, automation proposals, CI setup, and optional Obsidian Vault creation with approval gates. Global and project-agnostic. Trigger when the user says "bootstrap project", "bootstrap-project", "set up this repo for Codex", "onboard this project", or "run the full project setup". Use when this capability is needed.
metadata:
  author: ada-ggf25
---

# Bootstrap A Project For Codex

Run the Codex-oriented new-repo setup workflow in one guided pass. This is a thin
orchestrator: it owns sequencing and handoffs, and delegates detailed work to the
focused skills.

Sequence:

`AGENTS.md` setup -> `where-agents-md` -> `setup-permissions` ->
`propose-automation` -> `setup-ci` optional -> `obsidian-vault` optional ->
durable memory suggestions.

## Operating Principles

1. Orchestrate, do not reimplement delegated skills.
2. Pause after each step and continue only after user approval.
3. Be idempotent: detect existing `AGENTS.md`, `.codex/`, `.agents/`, and repo config.
4. Prefer shared repo guidance only when it is useful to teammates; keep personal Codex
   config personal.

## Procedure

### 0. Orient

- Confirm the working directory is the intended repo root.
- Detect existing setup: root/nested `AGENTS.md`, `AGENTS.override.md`, `.codex/`,
  `.agents/`, `CLAUDE.md`, README, CI, and project manifests.
- For a large repo, delegate broad exploration to a read-only subagent when available.
- Show the planned sequence and let the user drop steps.

### 1. Root Codex Instructions

- If no root `AGENTS.md` exists, propose creating one from observed repo facts.
- If one exists, offer to review/update it instead of regenerating.
- If `CLAUDE.md` exists, use it as source context but do not copy Claude-only commands,
  hook syntax, or permission syntax blindly.

### 2. Scoped Instruction Placement

- Invoke `where-agents-md` to identify high-value nested `AGENTS.md` candidates.
- Present recommendations and create files only for directories the user approves.

### 3. Codex Permissions And Config

- Invoke `setup-permissions` to review Codex sandbox, trusted project, and command
  approval expectations.
- Do not edit global `~/.codex/config.toml` unless the user explicitly asks. Prefer
  project-scoped guidance or a patch proposal.

### 4. Automation Proposal

- Invoke `propose-automation` to suggest project Codex skills, custom agents, hooks, or
  plugin packaging.
- Scaffold only approved items.

### 5. CI Pipeline

- Optionally invoke `setup-ci` for GitHub Actions. Skip if the repo is not on GitHub or
  if the user declines.

### 6. Obsidian Vault

- Optionally invoke `obsidian-vault` if the user wants a repo knowledge base.

### 7. Durable Memories

- Offer only genuinely durable, non-secret facts as project memories. Do not record
  information already captured in `AGENTS.md` or README.

### 8. Wrap Up

- Summarize created/changed files.
- Remind the user that new Codex skills and agents are picked up in a new Codex session.

## Guardrails

- Never run the full chain unattended.
- Never clobber existing setup; merge or propose targeted edits.
- Keep Claude and Codex artifacts separate unless the user explicitly asks for a shared
  compatibility file.
- Never fabricate repo facts.

---
> Source: [ada-ggf25/AI-Tools](https://github.com/ada-ggf25/AI-Tools) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
