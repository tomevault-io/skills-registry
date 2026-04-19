---
name: skill-creator
description: Create a new Claude Code skill in this project with proper structure, instructions, and supporting files Use when this capability is needed.
metadata:
  author: levkiss
---

# Create New Skill: `$ARGUMENTS`

Build a complete, production-quality Claude Code skill.

---

## Phase 1: Gather Requirements

Before writing anything, ask the user:

1. **Purpose** — What does this skill do? (one sentence)
2. **Trigger** — Should it be:
   - User-invocable via `/skill-name`? (default: yes)
   - Auto-invocable by Claude when relevant? (default: yes)
   - User-only, never auto-invoked? (for dangerous/side-effect actions)
3. **Arguments** — Does it take input? What format?
4. **Tools needed** — Which tools should it have access to? (Read, Edit, Write, Bash, Grep, Glob, etc.)
5. **Complexity** — Does it need supporting files (templates, examples, scripts)?

---

## Phase 2: Create File Structure

```bash
mkdir -p .claude/skills/$ARGUMENTS
```

### Simple skill (single file):

```
.claude/skills/$ARGUMENTS/
└── SKILL.md
```

### Complex skill (with supporting files):

```
.claude/skills/$ARGUMENTS/
├── SKILL.md              # Main instructions (required)
├── templates/            # Output templates for Claude to fill
│   └── output.md
├── examples/             # Example inputs/outputs for quality
│   ├── good-example.md
│   └── bad-example.md
└── scripts/              # Helper scripts referenced in instructions
    └── validate.sh
```

---

## Phase 3: Write SKILL.md

### Frontmatter Reference

```yaml
---
name: skill-name                    # Lowercase, hyphens, max 64 chars
description: >-                     # Multi-line description for auto-invocation
  What this skill does and when Claude should use it.
  Be specific so Claude matches the right user requests.
argument-hint: [arg1] [arg2]        # Shown in autocomplete
allowed-tools: Read, Edit, Bash     # Comma-separated tool access
disable-model-invocation: false     # true = user-only (no auto-invoke)
user-invocable: true                # false = hidden from / menu
model: default                      # or: opus, sonnet, haiku
context: inline                     # or: fork (isolated subagent)
agent: general-purpose              # only with context: fork
---
```

### Frontmatter Decision Guide

| Scenario | Settings |
|----------|----------|
| Normal slash command | `user-invocable: true`, `disable-model-invocation: false` |
| Dangerous action (deploy, delete) | `disable-model-invocation: true` |
| Background knowledge (not a command) | `user-invocable: false` |
| Heavy research task | `context: fork`, `agent: Explore` |
| Read-only investigation | `allowed-tools: Read, Grep, Glob` |

### Markdown Body Structure

Every skill body should follow this pattern:

```markdown
# Skill Title

One-line summary of what this skill does.

---

## Context
What the user is trying to achieve and when this skill is appropriate.

## Inputs
- `$ARGUMENTS` — all arguments as a string
- `$0`, `$1`, `$2` — positional arguments
- `${CLAUDE_SESSION_ID}` — current session ID (for logging)

## Instructions
Step-by-step numbered instructions Claude will follow.
Be explicit — Claude follows these literally.

1. **Step one** — what to do first
2. **Step two** — what to do next
3. ...

## Constraints
- What Claude must NOT do
- Boundaries and safety rails
- Edge cases to handle

## Output Format
What the final output should look like.
Reference a template file if complex.

## Examples (optional, inline)
Show a concrete input → output example so Claude understands quality expectations.
```

---

## Phase 4: Write Quality Instructions

### Good vs Bad Instructions

**BAD — vague:**
```markdown
Review the code and give feedback.
```

**GOOD — specific and actionable:**
```markdown
## Instructions

1. Read all files changed in the PR using `gh pr diff $0`
2. For each file, check:
   - Dark mode: every element has `dark:` variant classes
   - Responsive: uses `sm:`, `md:`, `lg:` breakpoints
   - Accessibility: semantic HTML, ARIA labels on interactive elements
   - Performance: no unnecessary re-renders, images optimized
3. List issues as a numbered checklist grouped by severity:
   - **Critical**: broken functionality, accessibility violations
   - **Warning**: missing dark mode, non-responsive elements
   - **Suggestion**: style improvements, refactoring opportunities
4. If no issues found, confirm with "All checks passed."
```

**BAD — no constraints:**
```markdown
Deploy the site.
```

**GOOD — safe with guardrails:**
```markdown
## Constraints
- NEVER force-push to main
- NEVER deploy if tests are failing
- ALWAYS show the user what will be deployed and ask for confirmation
- If the working directory has uncommitted changes, warn and stop
```

---

## Phase 5: Add Supporting Files (if needed)

### Template Example

`templates/output.md`:
```markdown
# Review: $ARGUMENTS

## Summary
[One-line summary]

## Issues Found
| # | Severity | File | Line | Description |
|---|----------|------|------|-------------|
| 1 | Critical | | | |

## Recommendations
- [Recommendation 1]

## Verdict
[ ] Approved  [ ] Needs changes
```

Reference in SKILL.md:
```markdown
Use the template in `templates/output.md` to structure your response.
```

### Example Files

`examples/good-example.md` — show Claude what quality output looks like:
```markdown
# Input
/review-pr 42

# Expected Output
## Review: PR #42 — Add blog pagination

### Summary
Adds client-side pagination to blog.html with 6 posts per page.

### Issues Found
| # | Severity | File | Line | Description |
|---|----------|------|------|-------------|
| 1 | Warning | blog.html | 45 | Missing `dark:` class on pagination buttons |
| 2 | Suggestion | blog.html | 12 | Consider `aria-label` on page number buttons |

### Verdict
Needs changes — fix dark mode on pagination controls.
```

`examples/bad-example.md` — show Claude what to avoid:
```markdown
# Bad Output (avoid this)
"Looks good to me, no issues." ← Too vague, no evidence of review

# Also Bad
A 500-line essay about every HTML tag ← Too verbose, no prioritization
```

---

## Phase 6: Validate

After creating the skill, verify:

1. **File exists**: `.claude/skills/$ARGUMENTS/SKILL.md` is present
2. **Frontmatter is valid YAML**: no syntax errors in the `---` block
3. **Name matches directory**: `name:` field matches the folder name
4. **Description is specific**: Claude can match it to user requests
5. **Instructions are numbered**: clear step-by-step, not a wall of text
6. **Constraints exist**: for any skill with side effects
7. **Tools are minimal**: only grant tools the skill actually needs
8. **Supporting files referenced**: if templates/examples exist, SKILL.md mentions them
9. **Under 500 lines**: if longer, split into supporting files

List the created files to confirm with the user.

---

## Complete Example: A Well-Structured Skill

Here's a full example of a skill you might create for this project:

### `.claude/skills/audit-page/SKILL.md`

```yaml
---
name: audit-page
description: >-
  Audit an HTML page for design system compliance, accessibility,
  dark mode coverage, and responsive design. Use when the user
  asks to review or check a page.
argument-hint: [page.html]
allowed-tools: Read, Grep, Glob
---

# Audit Page: $ARGUMENTS

Run a comprehensive quality check on the specified HTML page.

---

## Context

This project is a static portfolio site using Tailwind CSS via CDN,
vanilla JavaScript, and a component-based header/footer pattern.
All pages must follow the design system in `.claude/rules/design-system.md`.

## Instructions

1. **Read the file**: Read `$0` completely
2. **Check page template compliance**:
   - Has `<html lang="en" class="scroll-smooth">`
   - Has meta viewport tag
   - Includes Tailwind CDN, Iconify CDN, Google Fonts
   - Has inline Tailwind config matching other pages
   - Has `#header-placeholder` and `#footer-placeholder`
   - Includes `js/header.js` and `js/footer.js`
3. **Check dark mode coverage**:
   - Every `bg-` class has a `dark:bg-` counterpart
   - Every `text-` color class has a `dark:text-` counterpart
   - Every `border-` color class has a `dark:border-` counterpart
   - Glass panel elements use the `.glass-panel` class
4. **Check responsive design**:
   - Grid layouts use responsive columns (`grid-cols-1 md:grid-cols-2 lg:grid-cols-3`)
   - Typography scales with breakpoints
   - No horizontal overflow on mobile (check for fixed widths)
5. **Check accessibility**:
   - Semantic HTML (`<nav>`, `<main>`, `<section>`, `<article>`)
   - Images have `alt` attributes
   - Interactive elements are keyboard-accessible
   - Links have descriptive text (no "click here")
   - Color contrast is sufficient (not just color to convey info)
6. **Check design system**:
   - Colors match `.claude/rules/design-system.md`
   - Spacing follows the established scale
   - Typography uses Inter/JetBrains Mono
   - Animations use `cubic-bezier(0.25, 0.8, 0.25, 1)`
7. **Report findings** using the template below

## Constraints

- Do NOT modify the file — this is a read-only audit
- Do NOT flag Tailwind CDN usage as a problem (it's intentional)
- Do NOT suggest switching to a build tool or framework
- Focus only on actionable findings, not opinions

## Output Format

### Audit: [filename]

**Score**: X/10

| # | Severity | Category | Line | Finding |
|---|----------|----------|------|---------|
| 1 | ... | ... | ... | ... |

**Severity levels**: Critical > Warning > Info

**Summary**: 1-2 sentences on overall page quality.
```

---

## Skill Complexity Guide

| Complexity | When | Structure |
|------------|------|-----------|
| **Simple** | Single action, < 20 lines of instructions | SKILL.md only |
| **Medium** | Multi-step workflow, needs structure | SKILL.md + template |
| **Complex** | Needs examples, scripts, multiple outputs | SKILL.md + templates/ + examples/ |
| **Research** | Deep exploration, no side effects | Use `context: fork` + `agent: Explore` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/levkiss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
