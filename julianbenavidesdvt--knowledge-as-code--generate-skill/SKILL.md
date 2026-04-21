---
name: generate-skill
description: Use when working with a skill to create other Antigravity skills with the correct structure and best practices.
metadata:
  author: julianbenavidesdvt
---

# Generate Skill

This skill helps you generate new skills for the Antigravity system. Follow this process to standardise skill creation.

## Process

1.  **Understand the Goal**:
    *   Read the user's request to understand what the new skill should do.
    *   Ask clarifying questions if the scope is too broad.

2.  **Define Metadata**:
    *   **Name**: Choose a concise, snake_case name (e.g., `git_commit_helper`, `react_component_gen`).
    *   **Description**: A one-sentence summary of what the skill does.

3.  **Determine Tooling**:
    *   Does this skill need custom scripts (Python/Shell)? If so, plan for a `scripts/` folder.
    *   Does it need templates? Plan for a `templates/` or `resources/` folder.

4.  **Generate Files**:
    *   **Directory**: Create `skills/<skill_name>/`.
    *   **Main File**: Create `skills/<skill_name>/SKILL.md`.
    
    **SKILL.md Template**:
    ```markdown
    ---
    name: <skill_name>
    description: <description>
    ---
    
    # <Human Readable Title>
    
    <Detailed introduction of what this skill does>
    
    ## Instructions
    
    1.  Step 1...
    2.  Step 2...
    
    ## Best Practices
    - Tip 1...
    ```

5.  **Review**:
    *   Ensure the instructions in the new `SKILL.md` are explicit enough for an AI agent to follow without ambiguity.
    *   Check that the YAML frontmatter is valid.

## Example 

If the user wants a skill to "Audit security":
1.  Create `skills/security_audit/`.
2.  Create `skills/security_audit/SKILL.md`.
3.  The MD file should instruct the agent to look for sensitive keys, check dependencies, etc.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/julianbenavidesdvt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
