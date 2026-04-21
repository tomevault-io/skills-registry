---
name: create-new-skill
description: Specialized skill for creating new Antigravity agent skills. Follows a Research -> Create -> Deploy workflow where the Agent directly creates the necessary files. Use when this capability is needed.
metadata:
  author: calvyntwh
---

# Create New Skill

This skill guides the agent in creating a new Antigravity skill. It uses an **Agent-Native** workflow where the Agent directly researches the topic and creates the files, rather than relying on external scripts.

## When to use this skill
- The user explicitly asks to "create a skill", "make a new skill", or "add a skill".
- The user asks how to create a skill and wants you to do it for them.

## How to use it

### Creating a New Skill

1.  **Gather Information**:
    - Ask for the **Skill Name** (must be lowercase with hyphens, e.g., `code-review`, `postgres-helper`).
    - Ask for the **Scope** (Local Repo vs Global). *Default is Local Repo.*

2.  **Research Phase**:
    - **Search**: Perform a web search (`search_web`) using the skill topic to find:
        - Official documentation and key concepts.
        - Primary libraries, tools, or APIs involved.
        - Standard patterns, best practices, or existing solutions.
    - **Synthesize**: Note down key URLs and concepts to include in the skill's reference documentation.

3.  **Create Skill Files**:
    - **Method**: Use your `write_to_file` tool to directly create the necessary files.
    - **Location**: `<workspace-root>/.agent/skills/<skill-name>/`
    
    a.  **Create `SKILL.md`**:
        - **Format**: Must start with YAML frontmatter followed by Markdown.
        - **Frontmatter Rules** (Strict Compliance):
          - `name`: Required. Must match directory name. Lowercase `a-z`, `0-9`, `-` only. Max 64 chars. No consecutive hyphens.
          - `description`: Required. Max 1024 chars. Describe *what* it does and *when* to use it.
          - `license`: Optional (Recommended). e.g., `Apache-2.0` or `MIT`.
          - `metadata`: Optional. Key-value pairs for versioning/authorship.
          ```yaml
          ---
          name: <skill-name>
          description: <summary from research>
          license: MIT
          metadata:
            author: agent
            version: "1.0"
          ---
          ```
        - **Content Rules**:
          - Keep simple (< 500 lines).
          - Use `references/` for long documentation (Progressive Disclosure).
          - Include "Overview", "Methodology", "When to use", and "Examples".

    b.  **Create `references/research.md`**:
        - Create the `references/` directory.
        - Write the research notes, key concepts, and URLs found during the research phase.

4.  **Finalize**:
    - **No Scripts Needed**: You do NOT need to run any python scripts.
    - **Notify**: Inform the user the skill has been created in the repository.

### Validating a Skill

1.  **Manual Specification Check**:
    - **Action**: Verify the skill against the [Agent Skills Specification](https://agentskills.io/specification).
    - **Checklist**:
      - `name`: lowercase, hyphens only, matches directory name, 1-64 chars.
      - `description`: 1-1024 chars, describes "what" AND "when to use".
      - `license`: Present (optional but recommended).
    - **Fix Loop**: If any field is invalid, FIX the file and RE-VERIFY.

2.  **Manual Reality Check**:
    - **Read Back**: `view_file` the `SKILL.md`.
    - Does the `description` accurately reflect the content?
    - Is the `license: MIT` present?

### Packaging a Skill
*Not required for local development.*

## Rules
- **Safe Creation**: Check if the skill directory (`.agent/skills/<name>`) exists *before* creating. If it exists, **STOP** and ask the user if they want to overwrite.
- **Do NOT** use `uv`, `ruff`, or python scripts. This is an agent-native skill.
- **Do NOT** create files in `~/.gemini/antigravity/skills` directly. Always work in the repository.
- **Ensure** the `name` in the frontmatter matches the folder name (lowercase, hyphens).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/calvyntwh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
