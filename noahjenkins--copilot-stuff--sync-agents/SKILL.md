---
name: sync-agents
description: Synchronize AGENTS.md content into all detected AI coding agent configurations. AGENTS.md is the universal source of truth — read natively by GitHub Copilot (Aug 2025+), OpenAI Codex, Cursor, Gemini CLI, and VS Code. Falls back to .github/copilot-instructions.md for repos that have not migrated yet. Use when asked to mirror AGENTS.md or .github/copilot-instructions.md into Claude, Cursor, Gemini, Windsurf, and related tooling. Use when this capability is needed.
metadata:
  author: NoahJenkins
---

# Sync Agent Instructions, Agents, and Skills

Mirror all GitHub Copilot customizations — instructions, custom agents, and agent skills —
into every detected AI coding agent configuration found in this repository.

This skill supports **12 agent ecosystems**. Rather than creating files for every
agent unconditionally, it **detects which agents are already configured** in the repo and
syncs only those.

**Source locations:**

- `AGENTS.md` — global instructions **(primary source of truth)**
- `.github/copilot-instructions.md` — global instructions (fallback for repos not yet using AGENTS.md)
- `.github/instructions/*.instructions.md` — path-scoped instructions
- `.github/agents/*.agent.md` — custom agent profiles
- `.github/skills/<name>/SKILL.md` — agent skills (open standard)

## Your Task

Execute the following steps **in order**, creating or overwriting files as needed.
Never delete `AGENTS.md` or anything inside `.github/`.

---

### Step 1 — Read the source of truth

Read `AGENTS.md` at the repo root and store its full content as the source of truth.

If `AGENTS.md` is not found, fall back to `.github/copilot-instructions.md`. If neither exists, stop and tell the user:

> "No source file found. Create `AGENTS.md` at the repo root with your project instructions, then re-run `/sync-agents`."

Note which source file was used — it will be referenced in sync headers and reports.

Also scan and store the contents of:

- **`.github/instructions/`** — any `*.instructions.md` files. For each, record the filename, `applyTo` frontmatter glob (if present), and body content.
- **`.github/agents/`** — any `*.agent.md` files. For each, record the full filename, the complete YAML frontmatter block (`name`, `description`, `model`, `tools`, `handoffs`, etc.), and the body prompt.
- **`.github/skills/`** — any subdirectory containing a `SKILL.md` file. For each skill, record the directory name, the complete `SKILL.md` frontmatter (`name`, `description`, `user-invokable`, `disable-model-invocation`, etc.), the body content, and note any additional bundled asset files (scripts, examples, references) present in that directory.

---

### Step 2 — Detect active agents

Scan the repository root for the presence of the following files and directories.
For each one found, mark that agent as **active** and include it in subsequent sync steps.
**Do not create** directories or files for agents that are not already present.

| Agent               | Detection Signal(s)                                  | Category          |
| ------------------- | ---------------------------------------------------- | ----------------- |
| **Claude Code**     | `CLAUDE.md` at repo root **or** `.claude/` directory | Full sync         |
| **Gemini CLI**      | `GEMINI.md` at repo root **or** `.gemini/` directory | Full sync         |
| **OpenAI Codex**    | `.agents/` directory                                  | Full sync — **Note:** `AGENTS.md` is the source of truth for Codex instructions; no instructions sync needed. Detect only if `.agents/` directory is explicitly present. |
| **OpenCode**        | `.opencode/` directory **or** `.opencode.json`       | Full sync         |
| **Cursor**          | `.cursor/` directory **or** `.cursorrules` file      | Full sync         |
| **Windsurf**        | `.windsurf/` directory **or** `.windsurfrules` file  | Full sync         |
| **Cline**           | `.clinerules` file **or** `.clinerules/` directory   | Full sync         |
| **Roo Code**        | `.roo/` directory **or** `.roorules` file            | Full sync         |
| **Kilo Code**       | `.kilocode/` directory                               | Full sync         |
| **JetBrains Junie** | `.junie/` directory                                  | Full sync         |
| **Zed**             | `.rules` file at repo root                           | Instructions only |
| **Augment Code**    | `.augment/` directory                                | Full sync         |

If **no agents are detected**, offer the user a choice:

> "No agent configuration directories or files were detected in this repository. You have two options:
>
> **Option A — Create AGENTS.md (recommended for new repos):** `AGENTS.md` is now read by GitHub Copilot, OpenAI Codex, OpenCode, Cursor, Gemini CLI, and VS Code. Creating this file gives you broad multi-tool coverage from a single file. Confirm to create `AGENTS.md` now and sync your Copilot instructions into it.
>
> **Option B — Initialize a specific agent:** Create the config signal for the tool you use (e.g., `CLAUDE.md` for Claude Code, `.cursor/` for Cursor) and re-run `/sync-agents`."
>
> If the user confirms Option A, create `AGENTS.md` at the repo root with a sync header and the full content of the source file from Step 1 (`.github/copilot-instructions.md`), then skip to Step 6 and report completion. Note: once `AGENTS.md` is created, future runs will use it as the source of truth.

List the detected agents before proceeding:

```
🔍 Detected agents: <comma-separated list of detected agent names>
   Skipping (not detected): <comma-separated list of undetected agent names>
```

---

### Step 3 — Sync instructions to detected agents

For each agent marked as **active** in Step 2, sync the global instructions and any
path-scoped instructions using the agent-specific format described below.

Every synced file must include a **sync header** (as a comment appropriate to the format)
indicating:

- The file is auto-synced and should not be edited directly
- The source of truth path (`AGENTS.md` or the specific instructions file)
- How to re-sync (`/sync-agents`)

---

#### 3A — Claude Code

**Global instructions → `CLAUDE.md`**

Create or overwrite `CLAUDE.md` at the repo root:

```markdown
<!--
  ╔══════════════════════════════════════════════════════════════╗
  ║  AUTO-SYNCED — DO NOT EDIT THIS FILE DIRECTLY               ║
  ║  Source of truth: AGENTS.md                                 ║
  ║  To update: run /sync-agents                                ║
  ╚══════════════════════════════════════════════════════════════╝
-->

# Claude Code Instructions

> **Source of truth:** [`AGENTS.md`](AGENTS.md)
>
> This file is automatically generated by the `/sync-agents` command.
> To propagate changes, update `AGENTS.md` and re-run that command.

---

[INSERT FULL CONTENT OF AGENTS.md HERE]
```

**Path-scoped instructions → `.claude/instructions/`**

If path-specific `.github/instructions/*.instructions.md` files exist, create matching
files in `.claude/instructions/` with a sync header comment. Strip the `applyTo` frontmatter
(Claude Code does not use it) but preserve it as a markdown comment for context.

---

#### 3B — Gemini CLI

**Global instructions → `GEMINI.md`**

Gemini CLI supports `@path/to/file.md` import syntax for dynamic loading at runtime.

```markdown
<!--
  ╔══════════════════════════════════════════════════════════════╗
  ║  SOURCE OF TRUTH: AGENTS.md                                 ║
  ║  This file uses Gemini CLI's native @import syntax so it    ║
  ║  always loads the latest instructions at runtime.           ║
  ║  Run /sync-agents to update path-specific files.            ║
  ╚══════════════════════════════════════════════════════════════╝
-->

# Gemini CLI Instructions

> **Source of truth:** `AGENTS.md`
> This file dynamically imports from `AGENTS.md` using Gemini's
> `@file` syntax. Any edits to `AGENTS.md` are reflected
> here automatically — no manual sync required.

@AGENTS.md
```

**Path-scoped instructions:** Append additional `@` imports, one per line. If any have
`applyTo` targeting a subdirectory, create matching `GEMINI.md` files in those directories
using the same `@` import syntax pointing back to `.github/instructions/`.

---

#### 3C — AGENTS.md (Universal)

**AGENTS.md is the source of truth — no sync needed.**

> `AGENTS.md` is read natively by GitHub Copilot (Aug 2025+), OpenAI Codex, OpenCode, Cursor, Gemini CLI, and VS Code. Because it is the source of truth, no copy is written here. All other sync steps distribute FROM this file.

**Path-scoped instructions:** For each `.github/instructions/*.instructions.md` with an
`applyTo` targeting a subdirectory, create a corresponding `AGENTS.md` inside that subdirectory.
Codex reads one `AGENTS.md` per directory level and merges top-down.

Skip to Step 3D.

---

#### 3D — OpenCode

OpenCode reads `AGENTS.md` at the repo root directly. Since `AGENTS.md` is the source of truth, no additional sync step is needed — OpenCode picks it up automatically.

**Custom commands:** If `.github/skills/` contains skills, optionally create matching
`.opencode/commands/<skill-name>.md` files containing the skill's body content as a
reusable prompt. Prepend each with a sync header comment.

---

#### 3E — Cursor

**Global instructions → `.cursor/rules/global-instructions.mdc`**

Cursor uses `.mdc` files (Markdown with YAML frontmatter) in `.cursor/rules/`.

```markdown
---
# AUTO-SYNCED from AGENTS.md — do not edit directly
# Source of truth: AGENTS.md | Re-sync: /sync-agents
description: "Global project instructions synced from GitHub Copilot"
alwaysApply: true
---

[INSERT FULL CONTENT OF AGENTS.md HERE]
```

**Path-scoped instructions → `.cursor/rules/<name>.mdc`**

For each `.github/instructions/<name>.instructions.md`, create `.cursor/rules/<name>.mdc`:

- Set `description` from the filename or first heading
- Set `globs` from the `applyTo` frontmatter value (if present)
- Set `alwaysApply: false` (the glob handles activation)
- Write the body content after the frontmatter

---

#### 3F — Windsurf

**Global instructions → `.windsurf/rules/global-instructions.md`**

Windsurf uses plain Markdown files in `.windsurf/rules/`.

```markdown
<!-- AUTO-SYNCED from AGENTS.md — do not edit directly -->
<!-- Source of truth: AGENTS.md | Re-sync: /sync-agents -->

# Global Project Instructions

[INSERT FULL CONTENT OF AGENTS.md HERE]
```

**Path-scoped instructions → `.windsurf/rules/<name>.md`**

For each `.github/instructions/<name>.instructions.md`, create a matching `.md` file
with sync header and body content.

---

#### 3G — Cline

**Global instructions → `.clinerules/global-instructions.md`**

Cline reads Markdown files from the `.clinerules/` directory. If only a `.clinerules` file
(not directory) exists, convert it to a directory first by moving the existing file to
`.clinerules/_original.md`, then proceed.

```markdown
<!-- AUTO-SYNCED from AGENTS.md — do not edit directly -->
<!-- Source of truth: AGENTS.md | Re-sync: /sync-agents -->

# Global Project Instructions

[INSERT FULL CONTENT OF AGENTS.md HERE]
```

**Path-scoped instructions → `.clinerules/<name>.md`**

For each `.github/instructions/<name>.instructions.md`, create a matching `.md` file.

---

#### 3H — Roo Code

**Global instructions → `.roo/rules/global-instructions.md`**

Roo Code reads `.md` files recursively from `.roo/rules/` in alphabetical order.

```markdown
<!-- AUTO-SYNCED from AGENTS.md — do not edit directly -->
<!-- Source of truth: AGENTS.md | Re-sync: /sync-agents -->

# Global Project Instructions

[INSERT FULL CONTENT OF AGENTS.md HERE]
```

**Path-scoped instructions → `.roo/rules/<name>.md`**

For each `.github/instructions/<name>.instructions.md`, create a matching `.md` file.

---

#### 3I — Kilo Code

**Global instructions → `.kilocode/rules/global-instructions.md`**

Kilo Code reads Markdown files from `.kilocode/rules/`.

```markdown
<!-- AUTO-SYNCED from AGENTS.md — do not edit directly -->
<!-- Source of truth: AGENTS.md | Re-sync: /sync-agents -->

# Global Project Instructions

[INSERT FULL CONTENT OF AGENTS.md HERE]
```

**Path-scoped instructions → `.kilocode/rules/<name>.md`**

For each `.github/instructions/<name>.instructions.md`, create a matching `.md` file.

---

#### 3J — JetBrains Junie

**All instructions → `.junie/guidelines.md`**

Junie uses a single `guidelines.md` file. Concatenate all instructions into one file.

```markdown
<!-- AUTO-SYNCED from AGENTS.md — do not edit directly -->
<!-- Source of truth: AGENTS.md | Re-sync: /sync-agents -->

# Project Guidelines

[INSERT FULL CONTENT OF AGENTS.md HERE]

---

<!-- Path-scoped instructions follow (if any) -->

[FOR EACH .github/instructions/*.instructions.md, INSERT:]

## [filename context / applyTo info]

[body content]
```

Since Junie only reads a single file, all path-scoped instructions are appended to the
same file with clear section headers.

---

#### 3K — Zed

**Global instructions → `.rules`**

Zed reads a `.rules` file at the project root. It is plain text (no frontmatter).

```
# AUTO-SYNCED from AGENTS.md — do not edit directly
# Source of truth: AGENTS.md | Re-sync: /sync-agents

[INSERT FULL CONTENT OF AGENTS.md HERE]
```

Zed also auto-detects `.cursorrules`, `.windsurfrules`, `AGENTS.md`, `CLAUDE.md`, and
`GEMINI.md` — so if those files were written by earlier steps, Zed gets additional coverage
automatically.

Path-scoped instructions are **not supported** by Zed's `.rules` format. If path-scoped
instructions exist, append a note:

```
# NOTE: Path-scoped instructions exist in .github/instructions/ but cannot be
# represented in Zed's .rules format. See those files for directory-specific guidance.
```

---

#### 3L — Augment Code

**Global instructions → `.augment/rules/global-instructions.md`**

Augment Code uses Markdown files with optional YAML frontmatter in `.augment/rules/`.

```markdown
---
# AUTO-SYNCED from AGENTS.md — do not edit directly
# Source of truth: AGENTS.md | Re-sync: /sync-agents
always_apply: true
---

# Global Project Instructions

[INSERT FULL CONTENT OF AGENTS.md HERE]
```

**Path-scoped instructions → `.augment/rules/<name>.md`**

For each `.github/instructions/<name>.instructions.md`, create `.augment/rules/<name>.md`:

- Set `always_apply: false` and `agent_requested: true` in the YAML frontmatter
- Include a `description` derived from the filename or `applyTo` value
- Write the body content after the frontmatter

---

### Step 3M — Model normalization policy (all targets)

Apply a single normalization policy for `model` fields across all detected targets.

For each detected target, determine whether the synced artifact format supports an explicit
agent/profile `model` field:

- **Supports explicit model field in this sync flow:** Claude Code (`.claude/agents/*.md`)
- **Does not support agent profile model fields in this sync flow:**
  Gemini CLI, OpenAI Codex, OpenCode, Cursor, Windsurf, Cline, Roo Code, Kilo Code,
  JetBrains Junie, Zed, Augment Code

When a target supports an explicit model field, normalize source `model` values to that
target's default family map before writing files.

**Default target map (current):**

| Target | Keep-as-is patterns | Rewrite patterns | Default fallback |
| ------ | ------------------- | ---------------- | ---------------- |
| Claude Code | `claude-*`, `sonnet`, `haiku`, `opus` | `gpt-*`, `o*`, `codex-*`, `gemini-*` | `sonnet` |

**Normalization rules:**

1. If model is target-native, keep unchanged.
2. If model is non-native but recognized, rewrite to target default mapped value.
3. If model is missing, inject target default mapped value.
4. If model is present but unrecognized, rewrite to target default and preserve provenance with a comment.

For targets that do not support agent profile models in this sync flow, do **not** inject
synthetic `model` fields. Record each one as `not applicable` in Step 6 reporting.

---

### Step 4 — Sync custom agents (where supported)

Custom agent profiles (`.github/agents/*.agent.md`) can only be synced to agents
that support sub-agent profiles. Currently, only **Claude Code** supports this.

**If Claude Code is active:**

For each `.github/agents/<name>.agent.md`:

1. Create `.claude/agents/<name>.md` (drop the `.agent` infix)
2. Copy YAML frontmatter and body, and apply **Step 3M model normalization policy** using the Claude target map.
3. Write the frontmatter, adding these YAML comments after `---`:
   ```
   # AUTO-SYNCED from .github/agents/<name>.agent.md — do not edit directly
  # Source of truth: .github/agents/ | Re-sync: /sync-agents
   ```
4. Write the body prompt verbatim after the closing `---`

**Model normalization rules (Copilot → Claude):**

- If `model` is already Claude-native (starts with `claude-`, or is `sonnet`, `haiku`, or `opus`), keep it unchanged.
- If `model` is Copilot/OpenAI/Gemini specific (examples: `gpt-5`, `gpt-5.3-codex`, `o3`, `o4-mini`, `gpt-4.1`, `gemini-*`, `codex-*`), rewrite it to `sonnet`.
- If `model` is missing, add `model: sonnet` in the synced `.claude/agents/<name>.md` frontmatter.
- If `model` is present but unrecognized, rewrite to `sonnet` and add a YAML comment directly above it:
  ```yaml
  # Normalized by /sync-agents from unsupported source model: <original-model>
  model: sonnet
  ```

**Default map reference:**

| Source model family | Synced Claude model |
| ------------------- | ------------------- |
| `claude-*`, `sonnet`, `haiku`, `opus` | Keep as-is |
| `gpt-*`, `o*`, `codex-*`, `gemini-*` | `sonnet` |
| Missing/unknown | `sonnet` |

**All other agents:** No sub-agent profile concept exists. Skip agent syncing.

---

### Step 5 — Sync agent skills (where supported)

Agent Skills use an open standard (`SKILL.md` with YAML frontmatter). Only tools with
a documented skill/command path receive synced copies.

| Tool                    | Skills path                      | Supported?           |
| ----------------------- | -------------------------------- | -------------------- |
| GitHub Copilot (source) | `.github/skills/<name>/SKILL.md` | ✅ Source            |
| Claude Code             | `.claude/skills/<name>/SKILL.md` | ✅ If detected       |
| OpenCode                | `.opencode/commands/<name>.md`   | ✅ Via Step 3D       |
| OpenAI Codex            | —                                | ❌ No skills concept |
| All others              | —                                | ❌ No skills concept |

For each `.github/skills/<skill-name>/` directory:

**If Claude Code is active:**

1. Create `.claude/skills/<skill-name>/` directory
2. Write `SKILL.md` verbatim with a YAML sync comment
3. Copy any bundled asset files as-is

**OpenCode** custom commands are handled in Step 3D. No additional action needed here.

**Important — `name` field constraint:** The `name` field in SKILL.md frontmatter must
exactly match the parent directory name. Do not rename directories or alter the field.

**Gemini CLI (if active):** Append a note to `GEMINI.md`:

```markdown
<!-- NOTE: Agent Skills are not supported by Gemini CLI. -->
<!-- Skills available in this repo: <comma-separated skill names> -->
<!-- See .github/skills/ for skill definitions usable by Copilot, Claude, and Codex. -->
```

**All others:** No skills concept. Skip.

---

### Step 6 — Verify and report

After writing all files, list every file created or updated, grouped by agent:

```
✅ Sync complete.

🔍 Detected agents: <list>
📁 Source of truth: AGENTS.md (agents and skills from .github/)

Files written:

  [Agent Name]
    - <file path>                              (<description>)
    ...

  [Agent Name]
    - <file path>                              (<description>)
    ...

  SKIPPED (not detected in repo):
    - <agent name>: <detection signal not found>
    ...

To keep all agents in sync, run /sync-agents after any update to AGENTS.md or .github/.
```

Also note:

```
💡 AGENTS.md is the source of truth: GitHub Copilot, OpenAI Codex, OpenCode,
   Cursor, Gemini CLI, and VS Code read it natively — no sync needed for those tools.
   All other tools above received synced copies.
```

Also include a model normalization subsection:

```text
🧠 Model normalization:

  Applied:
    - Claude Code: <N> agent file(s) normalized using target map

  Not applicable (no agent profile model field in this sync flow):
    - Gemini CLI
    - OpenAI Codex
    - OpenCode
    - Cursor
    - Windsurf
    - Cline
    - Roo Code
    - Kilo Code
    - JetBrains Junie
    - Zed
    - Augment Code
```

---

## Sync Coverage Reference

### Instructions

| Agent           | Primary File                      | Format                 | Sync Method         |
| --------------- | --------------------------------- | ---------------------- | ------------------- |
| GitHub Copilot  | `AGENTS.md` (read natively)       | Markdown               | **Source of truth** (no sync needed) |
| Claude Code     | `CLAUDE.md`                       | Markdown               | Full copy           |
| Gemini CLI      | `GEMINI.md`                       | Markdown + `@imports`  | Live import         |
| OpenAI Codex    | `AGENTS.md` (read natively)       | Markdown               | **Source of truth** (no sync needed) |
| OpenCode        | `AGENTS.md` (read natively)       | Markdown               | **Source of truth** (no sync needed) |
| Cursor          | `.cursor/rules/*.mdc`             | MDC (Markdown + YAML)  | Translated          |
| Windsurf        | `.windsurf/rules/*.md`            | Markdown               | Full copy           |
| Cline           | `.clinerules/*.md`                | Markdown               | Full copy           |
| Roo Code        | `.roo/rules/*.md`                 | Markdown               | Full copy           |
| Kilo Code       | `.kilocode/rules/*.md`            | Markdown               | Full copy           |
| JetBrains Junie | `.junie/guidelines.md`            | Markdown (single file) | Concatenated        |
| Zed             | `.rules`                          | Plain text             | Full copy           |
| Augment Code    | `.augment/rules/*.md`             | Markdown + YAML        | Translated          |

### Custom Agents

| Agent          | Path                        | Notes                             |
| -------------- | --------------------------- | --------------------------------- |
| GitHub Copilot | `.github/agents/*.agent.md` | **Source of truth**               |
| Claude Code    | `.claude/agents/*.md`       | Copied at sync; `model` normalized for Claude |
| All others     | ❌ Not supported            | No sub-agent profile concept      |

### Skills (Open Standard)

| Agent                   | Path                          | Notes               |
| ----------------------- | ----------------------------- | ------------------- |
| GitHub Copilot          | `.github/skills/<n>/SKILL.md` | **Source of truth** |
| Claude Code             | `.claude/skills/<n>/SKILL.md` | Verbatim copy       |
| OpenAI Codex / OpenCode | `.agents/skills/<n>/SKILL.md` | Verbatim copy       |
| All others              | ❌ Not supported              | No skills concept   |

---

## Docs references

- **GitHub Copilot** custom instructions: https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot
- **GitHub Copilot** custom agents: https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents
- **GitHub Copilot** agent skills: https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-skills
- **Claude Code** memory/instructions: https://docs.anthropic.com/en/docs/claude-code/memory
- **Claude Code** sub-agents: https://docs.anthropic.com/en/docs/claude-code/sub-agents
- **Gemini CLI** GEMINI.md + `@import`: https://google-gemini.github.io/gemini-cli/docs/cli/gemini-md.html
- **OpenAI Codex** AGENTS.md: https://developers.openai.com/codex/guides/agents-md/
- **OpenAI Codex** skills: https://developers.openai.com/codex/skills/
- **OpenCode** AGENTS.md + commands: https://opencode.ai/docs/customization
- **Cursor** rules: https://docs.cursor.com/context/rules-for-ai
- **Windsurf** rules: https://docs.windsurf.com/windsurf/customize#rules
- **Cline** custom instructions: https://docs.cline.bot/improving-your-workflow/cline-rules
- **Roo Code** custom instructions: https://docs.roocode.com/features/custom-instructions
- **Kilo Code** custom rules: https://kilo.ai/docs/features/custom-rules
- **JetBrains Junie** guidelines: https://www.jetbrains.com/help/junie/guidelines.html
- **Zed** rules: https://zed.dev/docs/ai/rules
- **Augment Code** rules: https://docs.augmentcode.com/using-augment/augment-rules
- **VS Code** custom agents (cross-tool format compatibility): https://code.visualstudio.com/docs/copilot/customization/custom-agents

---
> Source: [NoahJenkins/Copilot-Stuff](https://github.com/NoahJenkins/Copilot-Stuff) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
