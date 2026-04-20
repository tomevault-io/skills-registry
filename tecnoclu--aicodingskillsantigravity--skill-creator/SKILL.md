---
name: skill-creator
description: Use when working with a meta-skill that assists the agent in creating and scaffolding new skills by generating the standard directory structure and configuration files.
metadata:
  author: tecnoclu
---

# Skill Creator Instructions

This skill provides a standardized workflow for creating new skills within the agent's environment.

## Workflow: Create New Skill

When the user asks to create a new skill, follow these steps:

1.  **Gather Information**:
    *   **Skill Name**: Ask for a concise, human-readable name (e.g., "React Component Generator").
    *   **Directory Name**: Derive a `snake_case` directory name (e.g., `react_component_generator`).
    *   **Description**: Ask for a brief description of what the skill does.
    *   **Instructions**: Ask for the specific rules, scripts, or workflows the skill should enforce.

2.  **Create Directory Structure**:
    *   Skills are located in: `c:\Users\rgonz\OneDrive\Apps\skills\`
    *   Create the subdirectory: `c:\Users\rgonz\OneDrive\Apps\skills\[directory_name]`

3.  **Generate `SKILL.md`**:
    *   Create `c:\Users\rgonz\OneDrive\Apps\skills\[directory_name]\SKILL.md`.
    *   **Content Format**:
        ```markdown
        ---
        name: [Skill Name]
        description: [Description]
        ---

        # [Skill Name] Instructions

        [Instructions provided by the user]
        ```

4.  **Optional: Advanced Structure**:
    *   If the user has scripts or examples, create `scripts/` or `examples/` subdirectories as needed.

5.  **Verification**:
    *   Confirm the file exists.
    *   Inform the user the skill is ready and can be invoked by referencing it or its instructions.

## Tips for Writing Skills
*   **Clarity**: Instructions should be unambiguous.
*   **Context**: Specialize the skill for a specific domain (e.g., "Only use for Vue.js projects").
*   **Examples**: Providing examples in the `SKILL.md` is highly effective.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tecnoclu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
