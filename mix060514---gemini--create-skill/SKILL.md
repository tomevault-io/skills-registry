---
name: create-skill
description: Helper agent to scaffold new Gemini CLI skills. Use this when the user wants to "make a new skill", "add a skill", or "teach you a new capability" via the skill system. Use when this capability is needed.
metadata:
  author: mix060514
---

# Create Skill Agent

## Objective
To assist the user in defining and creating a new Agent Skill by generating the required folder structure and `SKILL.md` file.

## Workflow

1.  **Analyze Request**:
    *   Identify the desired **Skill Name** (should be `snake_case`).
    *   Identify the **Description** (Trigger condition).
    *   Identify the **Instructions/Rules** (What the skill actually does).
    *   *If any info is missing, ask the user or infer reasonable defaults based on context.*

2.  **Scaffold**:
    *   Target Directory: `.gemini/skills/<skill_name>/` (Project local) or `~/.gemini/skills/<skill_name>/` (Global, if requested). Defaults to Project local.
    *   Action: Create the directory using `mkdir -p`.

3.  **Generate Content**:
    Construct the `SKILL.md` content using this template:
    ```markdown
    ---
    name: <skill_name>
    description: <description>
    ---

    # <Skill Name>

    ## Goal
    <Detailed goal based on user input>

    ## Guidelines
    <The specific instructions provided by the user>
    ```

4.  **Execute**:
    *   Write the file using `write_file`.
    *   Notify the user that the skill is created and ready (might require CLI restart or reload depending on the system).

## Example
User: "Create a skill called 'python_refactor' that always enforces PEP8."
Action:
1. mkdir -p .gemini/skills/python_refactor
2. write_file .gemini/skills/python_refactor/SKILL.md with instructions to enforce PEP8.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mix060514) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
