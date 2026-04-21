---
name: skill-creation
description: Guide for creating Agent Skills in this repository. Use this when creating new skill(s) or documenting repeatable processes. Use when this capability is needed.
metadata:
  author: rubendguez
---

# SKILL: Creating Agent Skills

This document defines the standards and best practices for creating Agent Skills in this repository.

## Purpose

Agent Skills are folders of instructions, scripts, and resources that Copilot can load when relevant to improve its performance in specialized tasks. This SKILL teaches you how to create effective, well-structured skills for this project.

## Official Documentation

Always refer to the official GitHub documentation for the most up-to-date information:
- [About Agent Skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)

## File Location Requirements

### **CRITICAL: All project skills MUST be stored in:**
```
.github/skills/[skill-name]/SKILL.md
```

### Directory Structure
- Each skill gets its own subdirectory under `.github/skills/`
- Skill directory names **MUST** be:
  - Lowercase only
  - Use hyphens for spaces (kebab-case)
  - Match the `name` in the SKILL.md frontmatter
  - Descriptive and concise

**Examples:**
- ✅ `.github/skills/page-object-model/SKILL.md`
- ✅ `.github/skills/api-testing/SKILL.md`
- ✅ `.github/skills/skill-creation/SKILL.md`
- ❌ `.github/skills/PageObjectModel/SKILL.md` (not lowercase)
- ❌ `.github/skills/page_object_model/SKILL.md` (underscores, not hyphens)
- ❌ `skills/my-skill/SKILL.md` (wrong directory)

## SKILL.md File Structure

Every `SKILL.md` file **MUST** include:

### 1. YAML Frontmatter (MANDATORY - NEVER OMIT)

⚠️ **CRITICAL REQUIREMENT**: The YAML frontmatter section is **ABSOLUTELY MANDATORY** for every skill file. Without it, the skill will not be recognized or loaded by Copilot.

**NEVER create or update a skill without including the YAML frontmatter block at the very beginning of the file.**

```markdown
---
name: skill-name
description: Clear description of what the skill does and when Copilot should use it.
license: MIT (optional)
---
```

**Frontmatter Fields:**
- `name` (required): Unique identifier, lowercase, hyphens only
- `description` (required): When to use this skill and what it accomplishes
- `license` (optional): License information if applicable

**The frontmatter block MUST:**
- ✅ Be the **first** content in the file
- ✅ Start with exactly `---` on its own line
- ✅ End with exactly `---` on its own line
- ✅ Include both `name` and `description` fields
- ✅ Use valid YAML syntax

**Common mistakes to AVOID:**
- ❌ Placing markdown content before the frontmatter
- ❌ Omitting the frontmatter entirely
- ❌ Using incorrect YAML syntax
- ❌ Missing required `name` or `description` fields
- ❌ Forgetting the closing `---` delimiter

### 2. Markdown Body (Required)
- Clear, actionable instructions
- Step-by-step processes when applicable
- Code examples and templates
- Best practices and guidelines
- DO/DON'T lists for clarity

## Content Guidelines

### Structure Your SKILL Effectively

```markdown
---
name: example-skill
description: Brief description for Copilot to understand when to use this skill.
---

# SKILL: [Descriptive Title]

Brief introduction explaining the purpose.

## Purpose
Why this skill exists and what problems it solves.

## Core Principles
Main rules and patterns to follow.

### 1. First Principle
Detailed explanation with examples.

### 2. Second Principle
Detailed explanation with examples.

## Complete Example
Full working example demonstrating the skill.

## Best Practices

### DO
✅ List of things to do

### DON'T
❌ List of things to avoid

## Additional Resources
Links to relevant documentation or tools.
```

### Writing Effective Descriptions

The `description` field is critical—it determines when Copilot uses the skill.

**Good descriptions:**
- ✅ "Guide for creating Page Object Model classes. Use when creating or modifying page objects in tests/pages/"
- ✅ "Standards for API testing with Playwright. Use when creating or debugging API tests."
- ✅ "Step-by-step process for debugging GitHub Actions failures. Use when asked to debug failing workflows."

**Poor descriptions:**
- ❌ "Page objects" (too vague)
- ❌ "How to test" (not specific enough)
- ❌ "Documentation" (doesn't indicate when to use)

### Content Best Practices

**DO:**
✅ Be specific and actionable
✅ Include code examples with proper syntax highlighting
✅ Use clear hierarchical structure (headers, lists)
✅ Provide complete working examples
✅ Explain the "why" behind rules
✅ Keep language concise but comprehensive
✅ Use visual separators (lists, tables, code blocks)
✅ Include both positive (DO) and negative (DON'T) examples
✅ Reference official documentation when applicable
✅ Make instructions step-by-step when appropriate

**DON'T:**
❌ Be vague or ambiguous
❌ Assume prior knowledge without context
❌ Use overly complex language
❌ Create skills for one-time tasks
❌ Duplicate information across multiple skills
❌ Forget to update skills when processes change
❌ Make skills too broad (split into multiple skills if needed)

## Naming Conventions

### Skill Names (in frontmatter)
- Lowercase only
- Use hyphens, not underscores or spaces
- Be descriptive but concise
- Reflect the skill's purpose

**Examples:**
- `page-object-model`
- `api-testing-standards`
- `github-actions-debugging`
- `skill-creation`

### Directory Names
Must exactly match the skill name from frontmatter.

## When to Create a SKILL

Create a skill when:
- ✅ You have a repeatable process that should be followed consistently
- ✅ There are specific standards or patterns to enforce
- ✅ The task requires multiple steps in a particular order
- ✅ You want to document best practices for a specific domain
- ✅ New team members need guidance on a specific area
- ✅ There's a common task that could be automated or standardized

Don't create a skill for:
- ❌ One-time instructions
- ❌ Information better suited for README files
- ❌ General project documentation
- ❌ Simple tasks that don't need guidance

## Skills vs. Custom Instructions

**Use Skills for:**
- Detailed, specialized instructions
- Context-specific processes (e.g., debugging workflows)
- Instructions loaded only when relevant
- Processes with multiple steps or examples

**Use Custom Instructions for:**
- Simple, always-relevant guidance
- Project-wide coding standards
- General repository information
- Conventions that apply to almost every task

## Additional Resources

### Including Scripts and Resources

You can include additional files in your skill directory:

```
.github/skills/my-skill/
  ├── SKILL.md
  ├── examples/
  │   ├── example1.ts
  │   └── example2.ts
  └── scripts/
      └── helper.sh
```

Reference these in your SKILL.md:
```markdown
See the example in `examples/example1.ts` for a complete implementation.
```

## Complete Template

```markdown
---
name: your-skill-name
description: Clear description of when and why to use this skill.
---

# SKILL: Your Skill Title

Brief introduction.

## Purpose

Why this exists.

## Core Principles

### 1. First Principle
Details and examples.

## Complete Example

\`\`\`typescript
// Full working example
\`\`\`

## Best Practices

### DO
✅ Things to do

### DON'T
❌ Things to avoid

---

**Remember**: Keep it clear, actionable, and focused on repeatable processes.
```

## Validation Checklist

Before finalizing a skill, verify:
- [ ] **YAML frontmatter is present at the top of the file** ⚠️ CRITICAL
- [ ] **Frontmatter includes both `name` and `description` fields** ⚠️ CRITICAL
- [ ] **Frontmatter uses proper YAML syntax with opening and closing `---`** ⚠️ CRITICAL
- [ ] File is at `.github/skills/[skill-name]/SKILL.md`
- [ ] Directory name matches frontmatter `name` field
- [ ] Name is lowercase with hyphens only
- [ ] Description clearly states when to use the skill
- [ ] Content is well-structured with clear headers
- [ ] Includes concrete examples
- [ ] Has DO/DON'T guidance where applicable
- [ ] Uses proper Markdown formatting
- [ ] Code blocks have language specifiers
- [ ] Instructions are actionable and specific

---

**Remember**: 
1. **YAML frontmatter is NOT optional** - it's the mechanism that makes skills discoverable and usable by Copilot
2. Good skills make Copilot more effective at specialized tasks
3. Invest time in creating clear, comprehensive skills that will save time in the long run

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rubendguez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
