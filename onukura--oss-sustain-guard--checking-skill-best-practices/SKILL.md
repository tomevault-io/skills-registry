---
name: checking-skill-best-practices
description: Evaluates Claude skills against official best practices from Anthropic documentation. Use when reviewing skill quality, ensuring compliance with guidelines, or improving existing skills. Use when this capability is needed.
metadata:
  author: onukura
---

# Checking Skill Best Practices

Evaluates a skill against the latest official guidelines from Anthropic. Always fetches current documentation to ensure accurate, up-to-date assessment.

## When to Use

- Reviewing skill quality before finalization
- User asks to check compliance with best practices
- Improving or refactoring existing skills

## Evaluation Process

### 1. Fetch Latest Guidelines

**Start here every time:**

```
fetch_webpage("https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices")
```

Extract current evaluation criteria from the fetched content.

### 2. Read Target Skill

```bash
read_file(".claude/skills/[skill-name]/SKILL.md")
```

### 3. Evaluate Against Fetched Guidelines

Compare skill against criteria from the documentation:
- Core principles (conciseness, appropriate freedom, testing)
- Skill structure (frontmatter, naming, description)
- Content guidelines (terminology, time-sensitivity, patterns)
- Anti-patterns to avoid

### 4. Generate Report

Provide structured findings with specific recommendations:

## Evaluation Report Template

```markdown
## Skill Evaluation: [skill-name]

**Overall Score**: X/10
**Guideline Version**: [Date from fetched doc]

### ✅ Strengths
- [What follows best practices]

### ⚠️ Issues Found

#### Critical (Must Fix)
- [ ] [Issue with specific fix]

#### Recommended (Should Fix)
- [ ] [Improvement suggestion]

### 🔧 Actionable Steps
1. [Highest priority fix]
2. [Next improvement]

### 📚 Reference
[Relevant sections from fetched documentation]
```

## Usage Example

```
User: "Check if adding-new-metric follows best practices"

1. fetch_webpage(best-practices-url)
   → Extract current criteria

2. read_file(".claude/skills/adding-new-metric/SKILL.md")
   → Get skill content

3. Compare against extracted criteria:
   - Name format (gerund form?)
   - Description quality (what + when?)
   - Conciseness (≤500 lines?)
   - Progressive disclosure used?
   - Consistent terminology?

4. Generate report with specific fixes
```

## Key Evaluation Areas

From the fetched documentation, focus on:

**Critical:**
- YAML frontmatter correctness
- Naming convention compliance
- Description effectiveness

**Important:**
- Conciseness (every token justified?)
- Progressive disclosure (reference files?)
- Consistent terminology

**Code-specific (if applicable):**
- Unix-style paths
- Error handling
- MCP tool naming

## Iteration Pattern

1. Evaluate → 2. Report issues → 3. Apply fixes → 4. Re-evaluate

Use `multi_replace_string_in_file` for efficient corrections.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/onukura) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
