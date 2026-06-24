---
name: make-instruction
description: Create GitHub Copilot instruction files with proper applyTo patterns and prescriptive rules. Use when asked to "create instruction", "make instruction", "scaffold instruction", or when building file-type-specific rules that should always be active. Generates .instructions.md files with YAML frontmatter and DO/DONT patterns. Use when this capability is needed.
metadata:
  author: Luscii
---

# Make Instruction

Create properly structured GitHub Copilot instruction files. Instructions are always-active prescriptive rules that auto-load for specific file types.

## When to Use This Skill

- User asks to "create an instruction", "make instruction file", "scaffold instruction"
- Need to define mandatory rules for specific file types
- Want to enforce coding standards automatically
- Creating DO's and DON'Ts that should always apply

## When NOT to Use (Use Skill Instead)

- ❌ Content is descriptive/educational (not prescriptive)
- ❌ Content exceeds 800 lines
- ❌ Should load on-demand, not automatically
- ❌ Contains extensive examples/tutorials

**See copilot.instructions.md for decision criteria**

## Prerequisites

- Clear understanding of rules to enforce
- File type pattern to apply to (`applyTo`)
- Prescriptive content (DO's/DON'Ts)
- Target length: 100-800 lines

## Instruction vs Skill Decision Tree

```
Is content prescriptive (MUST/DON'T)?
├─ Yes → Should it auto-load for file types?
│   ├─ Yes → Is it <800 lines?
│   │   ├─ Yes → CREATE INSTRUCTION ✅
│   │   └─ No → Use SKILL instead (too large)
│   └─ No → Use SKILL instead (on-demand better)
└─ No (descriptive) → Use SKILL instead
```

## Creating a New Instruction

### Step 1: Choose Location and Name

**Location:** `.github/instructions/{name}.instructions.md`

**Naming Convention:**
- Lowercase with hyphens
- Must end with `.instructions.md`
- Descriptive of scope

**Examples:**
- `terraform.instructions.md`
- `documentation.instructions.md`
- `python-style.instructions.md`
- `api-security.instructions.md`

### Step 2: Define applyTo Pattern

**Required:** Every instruction MUST have an `applyTo` pattern

**Pattern Types:**

| Pattern | Example | When to Use |
|---------|---------|-------------|
| File extension | `**/*.tf` | All Terraform files |
| Multiple extensions | `**/*.{ts,tsx}` | TypeScript files |
| Directory + extension | `tests/**/*.py` | Python test files |
| Specific paths | `docs/adr/**/*.md` | ADR documents |
| Multiple patterns | `**/*.md,variables.tf` | Markdown + specific file |
| Global | `**` | Rare - all files |

**Examples:**

```yaml
# Terraform files only
applyTo: "**/*.tf"

# Test files
applyTo: "**/*.tftest.hcl,tests/**/*.tf"

# Documentation
applyTo: "**/*.md,variables.tf,outputs.tf"

# Examples directory
applyTo: "examples/**/*,README.md"

# Specific subdirectory
applyTo: "docs/adr/**/*.md"

# Global (use sparingly!)
applyTo: "**"
```

### Step 3: Write the Frontmatter

**Required Format:**

```yaml
---
applyTo: "file/pattern/**/*.ext"
---
```

**Complete Example:**

```yaml
---
applyTo: "**/*.py"
---

# Python Coding Standards

## Quick Reference

[Content here]
```

### Step 4: Structure the Content

**Recommended Sections:**

```markdown
---
applyTo: "pattern"
---

# Instruction Name

## Quick Reference

[One-line summary of key rules]

**Cross-references:**
- Related skill → Use the **skill-name** skill

---

## Required Format/Structure

[Mandatory patterns and formats]

## Naming Conventions

✅ **DO:**
- Use descriptive names
- Follow snake_case for variables

❌ **DON'T:**
- Use single-letter variables
- Mix naming conventions

## Code Style

⚠️ **ALWAYS:**
- Use 2-space indentation
- Add docstrings to functions

❌ **NEVER:**
- Commit commented-out code
- Use wildcard imports

## Best Practices Checklist

- [ ] Item 1
- [ ] Item 2

## Common Mistakes to Avoid

1. **Mistake:** Description
   **Fix:** Solution

## Additional Resources

- **Related Skill:** skill-name
- External docs: [Link]
```

### Step 5: Keep It Concise

**Size Guidelines:**

| Lines | Status | Action |
|-------|--------|--------|
| <100 | Too small | Add to existing instruction or expand |
| 100-500 | ✅ Perfect | Sweet spot |
| 500-800 | ⚠️ Large | Consider if all content is essential |
| 800-1000 | 🔴 Too large | Refactor - move details to skill |
| >1000 | 🚫 Critical | MUST convert to skill |

**Refactoring Example:**

```markdown
# BEFORE (900 lines - TOO LARGE)
---
applyTo: "**/*.tf"
---

# Terraform Instructions

## Rules (200 lines)
## Detailed Explanations (400 lines)
## Examples (300 lines)

# AFTER - Split into instruction + skill

# terraform.instructions.md (300 lines)
---
applyTo: "**/*.tf"
---

## Rules

✅ DO: Use module.label.id
❌ DON'T: Hardcode names

**For detailed explanations, see the **terraform-resources** skill**

# terraform-resources skill (600 lines)
- Comprehensive examples
- Deep explanations
- Best practices
```

## Content Guidelines

### Prescriptive Language

**Use:**
- ✅ DO / DON'T
- ⚠️ ALWAYS / NEVER
- 🔴 MUST / MUST NOT
- ✔️ REQUIRED / FORBIDDEN
- 💡 RECOMMENDED / DISCOURAGED

**Examples:**

```markdown
✅ **DO:**
- Use type hints in Python functions
- Write unit tests for all public methods

❌ **DON'T:**
- Use `print()` for logging
- Commit `.env` files

⚠️ **ALWAYS:**
- Run `terraform fmt` before committing
- Add description to all variables

🔴 **MUST:**
- Pass security scans before merging
- Follow semver for releases

❌ **NEVER:**
- Hardcode credentials
- Disable security features

💡 **RECOMMENDED:**
- Use virtual environments for Python
- Enable pre-commit hooks
```

### Cross-References

**Pattern:** Link to skills for deep dives

```markdown
## Dynamic Blocks

**Quick Rule:** Use `dynamic` for repeating blocks

**For comprehensive examples and syntax details,
see the **terraform-resources** skill**
```

### Quick Reference Section

**Include at top:**

```markdown
## Quick Reference

**When writing Terraform code:**
- Use `module.label.id` for resource names
- Variables: alphabetical order, `context` first
- Outputs: alphabetical order
- Run: `terraform fmt`, `terraform validate`
- Security: `checkov -d .`

**Cross-references:**
- HCL syntax → Use the **terraform-syntax** skill
- Functions → Use the **terraform-functions** skill
```

## Templates

### Template 1: Code Style Instruction

```markdown
---
applyTo: "**/*.{ext}"
---

# [Language] Code Style Instructions

## Quick Reference

**When writing [Language] code:**
- Indentation: [X] spaces
- Line length: Max [X] characters
- Naming: [convention]

**Cross-references:**
- Syntax → Use the **[language]-syntax** skill

---

## Formatting

✅ **DO:**
- Use [X]-space indentation
- Align [specific elements]

❌ **DON'T:**
- Mix tabs and spaces
- Exceed [X] character lines

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Variables | snake_case | `user_name` |
| Functions | camelCase | `getUserData` |
| Classes | PascalCase | `UserModel` |

## Required Patterns

⚠️ **ALWAYS:**
- Add docstrings/comments
- Use type hints/annotations

❌ **NEVER:**
- Use magic numbers
- Ignore linter warnings

## Checklist

- [ ] Code formatted with [tool]
- [ ] All tests pass
- [ ] Linter shows no errors
```

### Template 2: Domain-Specific Instruction

```markdown
---
applyTo: "specific/path/**/*.ext"
---

# [Domain] Instructions

## Quick Reference

[Key rules summary]

**Cross-references:**
- Details → Use the **[domain]-guide** skill

---

## Required Structure

[Mandatory format/organization]

## DO's and DON'Ts

✅ **DO:**
- [Specific good practice]

❌ **DON'T:**
- [Specific anti-pattern]

## Validation

**Before committing:**
- [ ] Validation check 1
- [ ] Validation check 2

## Common Mistakes

1. **Issue:** Description
   **Solution:** Fix
```

### Template 3: Testing Instruction

```markdown
---
applyTo: "tests/**/*,**/*.test.*"
---

# Testing Instructions

## Quick Reference

**When writing tests:**
- Naming: `test_[feature]_[scenario]`
- Structure: Arrange-Act-Assert
- Coverage: Aim for 80%+

**Cross-references:**
- Patterns → Use the **test-organization-patterns** skill
- Assertions → Use the **test-assertions** skill

---

## Test Structure

✅ **DO:**
- Group related tests
- Use descriptive names
- Test edge cases

❌ **DON'T:**
- Write tests depending on order
- Skip error cases

## Naming

| Type | Pattern | Example |
|------|---------|---------|
| Unit tests | `test_unit_function_scenario` | `test_calculates_total` |
| Integration | `test_integration_feature` | `test_api_creates_user` |

⚠️ **ALWAYS:**
- Clean up test data
- Mock external dependencies

## Checklist

- [ ] All tests pass locally
- [ ] No test dependencies
- [ ] Coverage >80%
```

## Validation Checklist

Before committing an instruction:

- [ ] File ends with `.instructions.md`
- [ ] Has `applyTo` pattern in frontmatter
- [ ] applyTo pattern is valid glob syntax
- [ ] Content is prescriptive (DO's/DON'Ts)
- [ ] Length is 100-800 lines
- [ ] No extensive tutorials (those go in skills)
- [ ] Cross-references skills for details
- [ ] Quick reference section included
- [ ] Proper markdown formatting
- [ ] No trailing whitespace

## Common Issues

### Issue 1: Missing applyTo

```yaml
# ❌ WRONG
---
# No applyTo!
---

# ✅ CORRECT
---
applyTo: "**/*.tf"
---
```

### Issue 2: Too Large

```
Problem: Instruction is 1,200 lines

Solution:
1. Extract detailed explanations → Create skill
2. Keep only rules in instruction (200-300 lines)
3. Add cross-reference to new skill
```

### Issue 3: Descriptive Instead of Prescriptive

```markdown
# ❌ WRONG (Descriptive)
## How Dynamic Blocks Work

Dynamic blocks generate nested configurations...
[500 lines of explanation]

# ✅ CORRECT (Prescriptive)
## Dynamic Blocks

✅ **DO:**
- Use `dynamic` for repeating blocks
- Set `for_each` to collection

**For detailed examples, see the **terraform-resources** skill**
```

### Issue 4: Duplicate Content with Skill

```
Problem: 70% overlap with existing skill

Solution:
1. Remove duplicate content from instruction
2. Keep only prescriptive rules
3. Add cross-reference to skill
```

## Testing Your Instruction

After creating:

1. **Check activation:**
   - Open file matching applyTo pattern
   - Verify instruction is loaded

2. **Validate content:**
   - All rules are prescriptive
   - No lengthy explanations
   - Cross-references present

3. **Check length:**
   - File is 100-800 lines
   - If larger, refactor to skill

4. **Test with agent:**
   - Open matching file
   - Ask agent to follow rules
   - Verify rules are applied

## Migration from Skill

If you have a skill that should be an instruction:

```markdown
# BEFORE: terraform-basics skill (400 lines)
# Descriptive content + some rules

# AFTER: Split into instruction + skill

# terraform.instructions.md (200 lines)
- Prescriptive rules only
- Cross-reference to skill

# terraform-basics skill (600 lines)
- All explanations and examples
- Merged from both sources
```

## Examples from This Repository

### ✅ Good Instructions

| File | Lines | applyTo | Why Good |
|------|-------|---------|----------|
| terraform.instructions.md | 814 | `**/*.tf` | Clear rules, proper size |
| documentation.instructions.md | 418 | `**/*.md,...` | Prescriptive, focused |
| conventional-commits.instructions.md | 142 | `**` | Pure rules, no tutorial |

### ❌ Anti-Pattern

| File | Lines | Problem | Fix |
|------|-------|---------|-----|
| terraform-tests.instructions.md | 2,206 | Too large, descriptive | Convert to skill |

## Quick Start

**1. Decide if you need an instruction:**
   - See copilot.instructions.md decision tree

**2. Create the file:**
   ```bash
   touch .github/instructions/my-topic.instructions.md
   ```

**3. Add frontmatter:**
   ```yaml
   ---
   applyTo: "pattern/**/*.ext"
   ---
   ```

**4. Write prescriptive rules:**
   - DO's and DON'Ts
   - Keep under 800 lines
   - Cross-reference skills

**5. Validate:**
   - Test with matching files
   - Verify rules apply correctly

## References

- **copilot.instructions.md** - When to use instruction vs skill
- **make-skill-template** skill - Create skills instead
- See existing instructions for examples

---
> Source: [Luscii/terraform-fluentbit-luscii-presets](https://github.com/Luscii/terraform-fluentbit-luscii-presets) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
