---
name: skill-creator
description: Create new Claude Code skills. Use when the user wants to create a custom skill, slash command, or automation. Use when this capability is needed.
metadata:
  author: resultanyildizi
---

# Skill Creator

Help users create new Claude Code skills.

## Skill Structure

Skills live in `~/.claude/skills/<skill-name>/` with this structure:

```
~/.claude/skills/
  <skill-name>/
    skill.md          # Required: skill definition
    scripts/          # Optional: helper scripts
      script.py
      script.sh
```

## skill.md Format

```markdown
---
name: skill-name
description: Short description for Claude to know when to use this skill.
argument-hint: <arg1> [optional-arg] # Optional: shown in /help
allowed-tools: Bash(pattern), Read # Optional: restrict tool access
---

# Skill Title

Instructions for Claude when this skill is invoked.

## Usage

Document how to use the skill.

## Examples

Show example invocations.
```

## Frontmatter Fields

| Field           | Required | Description                            |
| --------------- | -------- | -------------------------------------- |
| `name`          | Yes      | Skill identifier (lowercase, hyphens)  |
| `description`   | Yes      | When Claude should use this skill      |
| `argument-hint` | No       | Usage hint shown in /help              |
| `allowed-tools` | No       | Restrict which tools the skill can use |

## Handling Secrets

**Never try to read secrets from files.** If a skill requires an API key or secret:

1. Document the required environment variable in the skill
2. If the secret is missing, tell the user to:
   - Set the environment variable (e.g., `export API_KEY=xxx`)
   - Restart Claude Code
3. Never attempt to read `.env`, `.zshrc.secret`, or similar files

Example section for skills needing secrets:

```markdown
## API Key

**Do not try to read secrets from files.** If the API key is not available, ask the user to:

1. Set the `YOUR_API_KEY` environment variable
2. Restart Claude Code
```

## Creating the Skill

When the user asks to create a skill:

1. Ask clarifying questions if needed:

   - What should the skill do?
   - Does it need external APIs? (if so, which env var?)
   - Should it have helper scripts?

2. Create the directory: `mkdir -p ~/.claude/skills/<name>`

3. Write `skill.md` with:

   - Clear frontmatter
   - Concise instructions
   - Usage examples
   - Secret handling (if applicable)

4. Create helper scripts in `scripts/` if needed

5. Test the skill by invoking it: `/<skill-name>`

## Examples

**Simple skill (no scripts):**

```
~/.claude/skills/greet/skill.md
```

**Skill with Python script:**

```
~/.claude/skills/image-gen/
  skill.md
  scripts/
    generate.py
```

**Skill with API key:**

```markdown
## API Key

**Do not try to read secrets from files.** If missing, ask user to:

1. Set `OPENAI_API_KEY` environment variable
2. Restart Claude Code
```

## Instructions

When creating a skill:

1. Confirm the skill name and purpose with the user
2. Create `~/.claude/skills/<name>/skill.md`
3. Add helper scripts if needed
4. Inform user the skill is ready to use with `/<name>`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/resultanyildizi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
