---
name: skill-creator
description: Guide for how to create a new agent skill in VS Code Use when this capability is needed.
metadata:
  author: kmkarakaya
---
# Skill Instructions

# How to [Create a skill](https://code.visualstudio.com/docs/copilot/customization/agent-skills#_create-a-skill)

Skills are stored in directories with a `SKILL.md` file that defines the skill's behavior. VS Code supports two types of skills:

* Project skills, stored in your repository: `.github/skills/` (recommended) or `.claude/skills/` (legacy, for backward compatibility)
* Personal skills, stored in your user profile: `~/.copilot/skills/` (recommended) or `~/.claude/skills/` (legacy, for backward compatibility)

To create a skill:

1. Create a `.github/skills` directory in your workspace.
2. Create a subdirectory for your skill. Each skill should have its own directory (for example, `.github/skills/webapp-testing`).
3. Create a `SKILL.md` file in the skill directory with the following structure:
   **Markdown**

   ```
   ---
   name: skill-name
   description: Description of what the skill does and when to use it
   ---

   # Skill Instructions

   Your detailed instructions, guidelines, and examples go here...
   ```
4. Optionally, add scripts, examples, or other resources to your skill's directory.
   For example, a skill for testing web applications might include:

   * `SKILL.md` - Instructions for running tests
   * `test-template.js` - A template test file
   * `examples/` - Example test scenarios

### [SKILL.md file format](https://code.visualstudio.com/docs/copilot/customization/agent-skills#_skillmd-file-format)

The `SKILL.md` file is a Markdown file with YAML frontmatter that defines the skill's metadata and behavior.

#### [Header (required)](https://code.visualstudio.com/docs/copilot/customization/agent-skills#_header-required)

The header is formatted as YAML frontmatter with the following fields:

| Field           | Required | Description                                                                                                                                                                                  |
| --------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`        | Yes      | A unique identifier for the skill. Must be lowercase, using hyphens for spaces (for example,`webapp-testing`). Maximum 64 characters.                                                      |
| `description` | Yes      | A description of what the skill does**and when to use it** . Be specific about both capabilities and use cases to help Copilot decide when to load the skill. Maximum 1024 characters. |

#### [Body](https://code.visualstudio.com/docs/copilot/customization/agent-skills#_body)

The skill body contains the instructions, guidelines, and examples that Copilot should follow when using this skill. Write clear, specific instructions that describe:

* What the skill helps accomplish
* When to use the skill
* Step-by-step procedures to follow
* Examples of the expected input and output
* References to any included scripts or resources

You can reference files within the skill directory using relative paths. For example, to reference a script in your skill directory, use `[test script](./test-template.js)`.

## [Example skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills#_example-skills)

The examples skills are  stored at C:\Codes\KMK_Chatbot\.github\skills\skill-creator\examples

## [Related resources](https://code.visualstudio.com/docs/copilot/customization/agent-skills#_related-resources)

* [Customize AI responses overview](https://code.visualstudio.com/docs/copilot/customization/overview)
* [Create custom instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)
* [Create reusable prompt files](https://code.visualstudio.com/docs/copilot/customization/prompt-files)
* [Create custom agents](https://code.visualstudio.com/docs/copilot/customization/custom-agents)
* [Agent Skills specification](https://agentskills.io/)
* [Reference skills repository](https://github.com/anthropics/skills)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kmkarakaya) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
