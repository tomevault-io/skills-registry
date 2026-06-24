---
name: skill-creator
description: Use this skill to create new Agent Skills for GitHub Copilot. It guides you through the process of setting up the directory structure and the SKILL.md file.
metadata:
  author: raphaelmansuy
---

# Skill Creator

This skill helps you create new Agent Skills for this repository. Agent Skills are specialized, repeatable tasks that GitHub Copilot can handle in agent mode.

## Why Agent Skills?

- **Specialized Workflows**: Handle tasks like debugging or testing without repetitive prompting.
- **Open Standard**: Uses the Anthropic standard for portability across tools (VS Code, Copilot CLI, Cursor).
- **Domain-Specific**: Injects repo-specific instructions and resources on demand.
- **Context Efficiency**: Reduces context window bloat by loading only relevant skills.

## Mental Model

Agent Skills are **repository-level extensions**.
- A **skill** is a directory under `.github/skills/`.
- Each skill contains a mandatory `SKILL.md` with YAML frontmatter (`name`, `description`) and a Markdown body.
- Copilot matches your request to a skill description and injects the content into its context.

## Steps to Create a New Skill

1.  **Choose a Name**: Use lowercase and hyphens (e.g., `my-new-skill`).
2.  **Create Directory**: Create a directory at `.github/skills/<skill-name>/`.
3.  **Create SKILL.md**: Create a `SKILL.md` file inside that directory.
4.  **Add Metadata**: Include YAML frontmatter with `name` and `description`.
    -   `name`: The name of the skill (same as the directory name).
    -   `description`: A clear, prompt-aligned description (e.g., "Use for debugging failing GitHub Actions").
5.  **Add Instructions**: Write the markdown body with clear steps, examples, and guidance.
6.  **Optional Resources**: Add scripts, data files, or tools in the same directory.

## Survival Kit

- **Day 0**: Install VS Code Insiders and enable Copilot.
- **Week 1**: Create your first skill and confirm it autoloads by writing a matching prompt.
- **Week 2**: Build production skills with scripts and share them in the team repo.

## Best Practices

- **One Job Per Skill**: Keep skills focused. Split them if they become too large.
- **Clear Descriptions**: Vague descriptions cause irrelevant loading.
- **No Secrets**: Never embed secrets; use environment variables.
- **Compactness**: Keep skills compact to save context.

## Example SKILL.md Template

```markdown
---
name: <skill-name>
description: <clear-description-of-the-skill>
---
<instructions-and-examples-go-here>
```

## Debugging & Observability

- Enable Copilot debug mode: `copilot.chat.debug=true`.
- Check the VS Code output panel logs for skill injection.
- Use `/explain` to see how the skill is being applied.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raphaelmansuy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
