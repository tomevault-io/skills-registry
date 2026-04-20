---
name: skill-hub
description: Use when working with a skill to discover, load, and use remote skills from the 'antigravity-awesome-skills' repository without permanent installation.
metadata:
  author: tecnoclu
---

# Skill Hub Instructions

This skill allows you to tap into the extensive collection of agentic skills hosted at `github.com/sickn33/antigravity-awesome-skills` on an "as-needed" basis.

## Workflow 1: Load Local Global Skills (Preferred)

Use this workflow to find and use skills stored locally in your global skills directory.

1.  **Search**:
    *   List the contents of the global skills directory: `C:\Users\rgonz\OneDrive\Apps\skills`
    *   Identify the directory that matches the requested skill name (e.g., `skill_creator`).

2.  **Load**:
    *   Read the `SKILL.md` file within that directory: `C:\Users\rgonz\OneDrive\Apps\skills\[skill_name]\SKILL.md`
    *   **Crucial**: Read the instructions in `SKILL.md` and apply them to your current task immediately.

## Workflow 2: Load a Remote Skill (On-Demand)

Use this workflow when you want to use a skill for the current task **without** installing it permanently.

1.  **Identify the Skill**:
    *   If the user names a specific skill (e.g., "Use the `agent-evaluation` skill"), proceed to step 2.
    *   If the user asks for a *type* of skill (e.g., "Do we have a skill for game dev?"), search the repository listing first.
        *   Repo URL: `https://github.com/sickn33/antigravity-awesome-skills/tree/main/skills`

2.  **Fetch the Instructions**:
    *   Construct the raw URL: `https://raw.githubusercontent.com/sickn33/antigravity-awesome-skills/main/skills/[skill-name]/SKILL.md`
    *   Use the `read_url_content` tool to read this URL.

3.  **Adopt the Skill**:
    *   Once the content is read, **immediately** acknowledge that you have "loaded" the skill.
    *   **Crucial**: Read the instructions in the fetched text and **apply them** to your current task. Treat the fetched content as if it were your primary instruction set for that specific sub-task.

## Workflow 3: Search for Remote Skills

If the user is unsure what skills are available:

1.  Read the repository file list: `https://github.com/sickn33/antigravity-awesome-skills/tree/main/skills`
    *   (Note: Use `read_url_content`. If that fails, ask the user to provide a specific skill name).
2.  Provide a summary of relevant skills to the user.

## Workflow 4: Install a Remote Skill (Global)

If the user explicitly asks to "install" or "save" a remote skill **globally** (for use in all projects):

1.  **Fetch**: Read the `SKILL.md` content as in Workflow 2.
2.  **Save**:
    *   Create a new directory: `c:\Users\rgonz\OneDrive\Apps\skills\[skill-name]`
    *   Write the content to `...\[skill-name]\SKILL.md`.
3.  **Confirm**: Notify the user that the skill is now permanently available globally.

## Workflow 5: Install a Remote Skill (Workspace-Only)

If the user explicitly asks to install a skill for **this project only** (or if the skill seems highly specific to the current project context):

1.  **Fetch**: Read the `SKILL.md` content as in Workflow 2.
2.  **Save**:
    *   Determine the Project Root.
    *   Create a new directory: `[ProjectRoot]\.agent\skills\[skill-name]`
    *   Write the content to `...\[skill-name]\SKILL.md`.
3.  **Confirm**: Notify the user that the skill is now available for this workspace only.

## Tips
*   Always try to "load" first unless the user insists on installation. This keeps the workspace clean.
*   If a remote skill references local scripts (e.g., `scripts/do_something.py`), "loading" might not work fully. In that case, warn the user and suggest installation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tecnoclu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
