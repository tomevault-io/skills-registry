---
name: skills-authoring
description: Create or edit a MiChat skill for the active profile (SKILL.md plus optional references/assets), following the Agent Skills spec and MiChat’s gating metadata. Use when this capability is needed.
metadata:
  author: filmicgaze
---

Use this skill when you need to create a new skill or revise an existing skill for the active profile.

## What a skill is and why it exists

In MiChat, a “skill” is a small, reusable instruction bundle that teaches the agent how to handle a particular kind of task (tools to use, rules to follow, and the preferred workflow). Skills are loaded into the model’s context only when relevant, so they improve reliability and consistency without bloating every conversation.

Use skills to:

* make recurring tasks repeatable (same tools, same safety boundaries, same workflow)
* keep “how to do this well” out of ad-hoc chat prompts
* keep the default context light — skills are pulled in only when needed

## Scope and ground rules

* Only edit skills that belong to the active profile (for example: `profiles/<profile>/skills/...`). Do not edit global skills.
* Stay within the skill’s folder boundary. Never write outside the profile’s `skills/` tree.
* MiChat does not execute bundled code. Do not create or rely on `scripts/`.

## Skill folder structure

A skill is a folder containing a required `SKILL.md` file.

Recommended minimum

* `profiles/<profile>/skills/<skill-name>/SKILL.md`

Optional (only when specifically requested)

* `profiles/<profile>/skills/<skill-name>/references/` (supporting docs)
* `profiles/<profile>/skills/<skill-name>/assets/` (templates, small resources)

## Tools

Read/inspect (core tools)

* `list_skills` — list installed skills (including scope/provenance).
* `read_skill` — read a skill’s `SKILL.md`.

Edit (skill_editing toolset)

* `skill_edit_list(skill_name, path?, max_items?, cursor?)` — list files/folders within a single skill directory. Useful for confirming what exists under `SKILL.md`, `references/`, and `assets/`.
* `skill_edit_write(skill_name, content, path?)` — write `SKILL.md` (default) or a file under `references/` or `assets/`.

Constraints (tool-enforced)

* Only the active profile’s skills are writable.
* Only `SKILL.md`, `references/…`, and `assets/…` are allowed. `scripts/` is blocked.
* Relative paths only; no `..` traversal; no absolute paths.
* No delete support.

## SKILL.md format

`SKILL.md` must start with YAML frontmatter between `---` lines, followed by Markdown instructions.

Required fields

* `name`: lowercase with hyphens (match the folder name).
* `description`: include when to use the skill (write it as “Use this when …”).

Recommended MiChat header

```md
---
name: my-skill-name
description: One sentence describing capability + when to use it.
metadata:
  michat.requires_toolsets: "documents"
---
```

## MiChat metadata conventions

Use `metadata.michat.requires_toolsets` to control whether a skill is available for a profile.

MiChat uses this field to filter **global** skills when required toolsets aren’t enabled; for **profile** skills it may be informational today, but keep it accurate for clarity and future compatibility.

* Value is a space-separated list of toolset names (for example: `"documents filesystem"`).
* Keep it minimal and truthful: list only the toolsets that are genuinely required to follow the skill.

## Preferred heading structure

Use consistent headings so skills are easy to scan and evolve:

* `## When to use this skill`
* `## Tools`
* `## Workflow`
* `## Constraints and safety`

Add additional `##` sections only when they earn their keep.

## Authoring workflow

1. Identify whether you’re editing a *profile skill* or creating a new one.

2. Draft the new `SKILL.md` content first (scratchpad is fine for drafting), then write it to disk.

3. Keep the body tight:
* Put the decision rule up front (“Use this skill when …”).

* Prefer short, operational instructions over long explanations.

* If a large example or background reference is useful, put it in `references/` (only if requested).
4. After writing, re-read the description and ask: “Would this description reliably trigger the right skill?” If not, sharpen it.

## What not to do

* Don’t add `scripts/` (MiChat won’t run them).
* Don’t introduce new toolsets in `michat.requires_toolsets` unless they’re actually required.
* Don’t make global policy changes here (this skill is for editing the active profile’s skills only).

## Quick quality checks

* Folder name matches `name`.
* Description includes a clear “Use this when …” trigger.
* The instructions can be followed without hidden assumptions.
* Toolset requirements are correct and minimal.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filmicgaze) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
