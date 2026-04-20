---
name: agent-to-opencode
description: Use this skill to generate OpenCode-compatible agent files in .opencode/agents/ from the English production sources. Converts AGENTS.md + SOUL.md content into OpenCode markdown format with proper frontmatter. Use when: 'generate opencode agents', 'deploy agents', 'update opencode', or after running agent-sync.
metadata:
  author: cesarszv
---

# Agent to OpenCode: English Sources → .opencode/agents/

## Overview

This skill converts English agent definitions from `resources/languages/en/` into OpenCode-compatible agent markdown files in `.opencode/agents/`. The English files are the **input** (production source). The OpenCode files are the **deploy target** that OpenCode actually loads at runtime.

## Pipeline Position

```
source/ (Spanish/Staging)
    ↓ agent-sync skill
resources/languages/en/ (English/Production)
    ↓ THIS SKILL
.opencode/agents/ (OpenCode/Deploy)
```

## The Process

### Step 1: Inventory English Sources

Read all English agent definitions:

```
resources/languages/en/source/<AgentName>/AGENTS.md
resources/languages/en/source/<AgentName>/SOUL.md    (if exists)
resources/languages/en/others/<AgentName>/AGENTS.md
```

For each agent, the **primary content** comes from AGENTS.md. If SOUL.md exists, its content is **merged** into the OpenCode file (SOUL.md provides personality depth that AGENTS.md may reference but not fully contain).

### Step 2: Determine Output Filename

Convert agent display name to kebab-case for the OpenCode filename:

| Agent Name | OpenCode File |
|-----------|---------------|
| Yupi Dupi | `yupi-dupi.md` |
| GentlemanClaude | `gentleman-claude.md` |
| Carmen Marin | `carmen-marin.md` |
| Diana | `diana.md` |
| Marty McBot | `marty-mcbot.md` |
| Sigmund Bot | `sigmund-bot.md` |
| Yurnal | `yurnal.md` |

**Naming rules:**
- Lowercase everything
- Replace spaces with hyphens
- Replace CamelCase with hyphen-separated (GentlemanClaude → gentleman-claude)
- No special characters

### Step 3: Build OpenCode Frontmatter

Every OpenCode agent file requires YAML frontmatter. Extract and construct it from the English source:

```yaml
---
description: <extracted from AGENTS.md frontmatter or first paragraph, MAX 120 chars>
mode: subagent
tools:
  write: true
  edit: true
  bash: <true or false based on agent type>
permission:
  bash:
    "*": <ask or deny>
color: "<hex color>"
---
```

**Field rules:**

| Field | Source | Rule |
|-------|--------|------|
| `description` | EN AGENTS.md frontmatter `description`, or first sentence of Personality/Overview section | Must be English, concise (max 120 chars), describe what the agent does |
| `mode` | Always `subagent` | All custom agents are subagents |
| `tools.write` | Always `true` | All agents can write |
| `tools.edit` | Always `true` | All agents can edit |
| `tools.bash` | Agent-specific | `true` for technical/code agents, `false` for documentation/PKM agents |
| `permission.bash."*"` | Derived from `tools.bash` | `ask` if bash enabled, `deny` if bash disabled |
| `color` | Assigned per agent | Use consistent color per agent (see color map below) |

**Color map:**
| Agent | Color | Hex |
|-------|-------|-----|
| Yupi Dupi | Orange-Red | `#FF5733` |
| GentlemanClaude | Green | `#33FF57` |
| Carmen Marin | Blue | `#3357FF` |
| Diana | Pink | `#FF33FF` |
| Marty McBot | Cyan | `#33FFFF` |
| Sigmund Bot | Yellow | `#FFFF33` |
| Yurnal | Amber | `#FF8833` |

For new agents, pick a color that doesn't conflict with existing ones.

### Step 4: Build Content Body

The body of the OpenCode file (after frontmatter) is assembled from English sources:

**If only AGENTS.md exists:**
1. Strip the YAML frontmatter from the English AGENTS.md
2. Use the remaining markdown content as-is

**If both AGENTS.md and SOUL.md exist:**
1. Start with SOUL.md content (strip its H1 title if present)
2. Append the Rules section from AGENTS.md (if not already in SOUL.md)
3. Deduplicate: if both files have the same section (e.g., both have `## Behavior`), prefer SOUL.md's version as it's more detailed

**Content ordering (preferred):**
1. Personality / Overview
2. Objective (if present)
3. Language
4. Tone
5. Philosophy
6. Experience / Expertise
7. Behavior
8. Rules
9. Technical Rules (if present)

### Step 5: Write OpenCode File

Write the assembled file to `.opencode/agents/<agent-name>.md`

### Step 6: Verify

For each generated file:
- [ ] Frontmatter is valid YAML
- [ ] `description` is present and in English
- [ ] `mode: subagent` is set
- [ ] Content is in English (no Spanish text)
- [ ] All major sections from the source are present
- [ ] File is in `.opencode/agents/` with correct kebab-case name

## OpenCode Agent File Template

```markdown
---
description: <concise English description, max 120 chars>
mode: subagent
tools:
  write: true
  edit: true
  bash: <true|false>
permission:
  bash:
    "*": <ask|deny>
color: "<#HEXCOLOR>"
---

## Personality
<from SOUL.md or AGENTS.md>

## Language
<from SOUL.md or AGENTS.md>

## Tone
<from SOUL.md or AGENTS.md>

## Philosophy
<from SOUL.md or AGENTS.md>

## Experience
<from SOUL.md or AGENTS.md>

## Behavior
<from SOUL.md or AGENTS.md>

## Rules
<from AGENTS.md>

## Technical Rules
<from AGENTS.md, if present>
```

## Key Principles

- **English only** - OpenCode agents are always in English; they derive from the English production layer
- **Content fidelity** - Don't rewrite or summarize; preserve the full content from sources
- **Deterministic output** - Same English sources must always produce the same OpenCode file
- **One file per agent** - Each agent is a single `.md` file in `.opencode/agents/`
- **No Spanish in deploy** - If you find Spanish text in a source, stop and run agent-sync first

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cesarszv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
