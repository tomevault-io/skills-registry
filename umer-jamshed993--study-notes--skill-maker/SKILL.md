---
name: skill-maker
description: Generate new Claude Code skills from user descriptions and requirements. Use when the user wants to create a new skill, build custom capabilities, or generate skill templates. Asks clarifying questions to fill gaps before generating complete SKILL.md files following Claude Code standards. Use when this capability is needed.
metadata:
  author: umer-jamshed993
---

# Skill Maker

Generate well-structured Claude Code skills from natural language descriptions while ensuring quality through clarifying questions.

## Instructions

When a user requests a new skill, follow this process:

### Step 1: Analyze the Request

Identify what information has been provided:
- Skill purpose/functionality
- When it should be triggered
- Required tools
- Target audience (personal or team)
- Input/output expectations
- Edge cases or constraints

### Step 2: Ask Clarifying Questions

Before generating any skill, you MUST ask questions to fill gaps. Common questions include:

**If purpose is vague:**
- "What specific problem should this skill solve?"
- "Can you give an example of when you'd use this skill?"

**If scope is unclear:**
- "Should this skill be personal (~/.claude/skills/) or project-wide (.claude/skills/)?"
- "What tools should this skill be allowed to use? (Read, Write, Bash, Grep, Glob, etc.)"

**If workflow is missing:**
- "What steps should the skill follow to complete its task?"
- "Are there any validation checks or error handling needed?"

**If examples are missing:**
- "Can you provide a sample input and expected output?"
- "What does success look like for this skill?"

**If constraints exist:**
- "Are there any file types, directories, or patterns to focus on or avoid?"
- "Should this skill integrate with any existing tools or workflows?"

### Step 3: Generate the Skill

Once all gaps are filled, generate a complete skill with:

1. **Valid YAML Frontmatter:**
   ```yaml
   ---
   name: skill-name-here
   description: What it does + when to use it (max 1024 chars)
   allowed-tools: Tool1, Tool2, Tool3
   ---
   ```

2. **Markdown Content Structure:**
   ```markdown
   # Skill Display Name

   ## Instructions
   Clear step-by-step guidance for Claude.

   ## Examples
   Concrete input/output examples.

   ## Best Practices
   Tips for effective usage.

   ## Requirements
   Any dependencies or prerequisites.
   ```

### Step 4: Save the Skill

Save the generated skill to the appropriate location:
- Personal: `~/.claude/skills/{skill-name}/SKILL.md`
- Project: `.claude/skills/{skill-name}/SKILL.md`

## Quality Checklist

Before finalizing any generated skill, verify:

- [ ] `name` field: lowercase, letters/numbers/hyphens only, max 64 chars
- [ ] `description` field: includes WHAT it does AND WHEN to use it
- [ ] `allowed-tools` field: only includes necessary tools
- [ ] Instructions section: clear, actionable steps
- [ ] Examples section: realistic input/output samples
- [ ] No tabs in YAML (spaces only)
- [ ] Proper `---` markers around frontmatter

## Examples

### Example Request:
```
"Create a skill that helps me write better commit messages"
```

### Clarifying Questions to Ask:
1. "Should this follow a specific convention like Conventional Commits?"
2. "Should it analyze staged changes or existing commit history?"
3. "Do you want it to suggest messages or validate existing ones?"
4. "Should this be a personal skill or shared with your team?"

### Example Generated Skill:
```yaml
---
name: commit-helper
description: Generate and improve git commit messages following Conventional Commits standards. Use when committing code changes, reviewing commit history, or improving documentation.
allowed-tools: Bash, Read, Grep
---

# Commit Helper

Help write clear, consistent commit messages following Conventional Commits.

## Instructions

1. Analyze staged changes using `git diff --staged`
2. Identify the type of change (feat, fix, docs, refactor, test, chore)
3. Determine the scope (component, module, or area affected)
4. Generate a concise subject line (max 50 chars)
5. Add detailed body if changes are complex
6. Include breaking change footer if applicable

## Examples

Input: User has staged a bug fix in the authentication module

Output:
```
fix(auth): resolve token expiration handling

- Add refresh token logic before API calls
- Handle edge case where token expires mid-request
- Update error messages for clarity
```

## Best Practices

- Keep subject line under 50 characters
- Use imperative mood ("add" not "added")
- Reference issue numbers when applicable
- Separate subject from body with blank line

## Requirements

- Git must be installed and repository initialized
- Changes should be staged before running
```

## Skill Naming Conventions

- Use lowercase letters, numbers, and hyphens only
- Keep names concise but descriptive
- Avoid generic names like "helper" or "tool"
- Use domain-specific prefixes when appropriate (e.g., "git-", "api-", "test-")

## Common Tool Permissions

Reference for `allowed-tools` field:

| Tool | Use Case |
|------|----------|
| Read | Reading files, configs, documentation |
| Write | Creating/updating files |
| Bash | Running commands, scripts, git operations |
| Grep | Searching file contents |
| Glob | Finding files by pattern |
| WebFetch | Fetching external documentation |
| AskUserQuestion | Gathering user input |

## Best Practices

1. **Always ask questions first** - Never assume missing requirements
2. **Keep skills focused** - One skill = one capability
3. **Write specific descriptions** - Claude uses these for discovery
4. **Include realistic examples** - Show actual usage patterns
5. **Test before sharing** - Verify the skill works as expected
6. **Document constraints** - Note any limitations or requirements

## Reference Documentation

For comprehensive implementation details, consult these reference documents:

- **[reference.md](reference.md)** - Complete clarification logic implementation including:
  - Requirement categories and prioritization
  - Question templates by category
  - Gap analysis algorithms
  - Skill generation templates (minimal, standard, comprehensive)
  - Validation rules and quality checklists
  - Common skill patterns (analyzer, generator, transformer, workflow)

- **[requirements-framework.md](requirements-framework.md)** - Structured requirements processing including:
  - Three-tier requirement model (Mandatory, Important, Enhancement)
  - Clarification engine with gap detection algorithms
  - Validation gates (pre-generation and post-generation)
  - Complete processing pipeline (Intake → Deliver)
  - Question templates library
  - Error handling and recovery strategies
  - Quality metrics and scoring

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/umer-jamshed993) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
