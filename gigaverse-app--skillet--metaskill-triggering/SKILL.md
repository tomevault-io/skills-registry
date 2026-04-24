---
name: metaskill-triggering
description: Optimize skill triggers and descriptions for reliable activation. Use when skill is not triggering, optimizing trigger keywords, writing frontmatter, debugging activation, or when user mentions "trigger", "frontmatter", "description", "skill not triggering", "optimize trigger", "skill won't fire", "skill activation", "trigger keywords". Use when this capability is needed.
metadata:
  author: gigaverse-app
---

# Skill Trigger Optimization

## Critical Insight

**ONLY the `description` field affects triggering.** All other frontmatter fields affect execution, not discovery.

At startup, Claude loads ONLY `name` + `description` (not full SKILL.md body). Matching is keyword-based, not deeply semantic.

## The Trigger Formula

```yaml
description: <What it does> + <Specific actions> + <When to use it> + <Trigger keywords>
```

### Example Breakdown

```yaml
# ✅ OPTIMAL - Hits all four parts
description: Write and review tests following TDD and best practices. ALWAYS use this skill BEFORE writing or modifying any test code. Triggers on "test", "tests", "testing", "TDD", "test-driven", "pytest", "add tests", "write tests", "test this", "unit test", "integration test", "test coverage", "bug fix", "fix bug", "verify", "edge case", or when about to create/edit files in tests/ directory.
```

| Part | Example Content |
|------|-----------------|
| What it does | "Write and review tests following TDD and best practices" |
| Specific actions | "BEFORE writing or modifying any test code" |
| When to use | "when about to create/edit files in tests/ directory" |
| Trigger keywords | "test", "tests", "testing", "TDD", "pytest"... |

## Keyword Best Practices

### Include Variants

```yaml
# ✅ All forms of the word
"test", "tests", "testing", "test-driven"

# ❌ Only one form - misses variations
"testing"
```

### Include User Phrases

```yaml
# ✅ Actual phrases users say
"add tests", "write tests", "test this", "TDD this"

# ❌ Only formal terms
"implement unit tests"
```

### Include Action Verbs

```yaml
# ✅ Action-oriented
"write", "create", "fix", "review", "optimize"

# ❌ Passive/noun-only
"tests", "code", "documentation"
```

### Include Slang and Abbreviations

```yaml
# ✅ How users actually talk
"TDD", "mf" (if appropriate), common abbreviations

# ❌ Only formal language
"test-driven development methodology"
```

## Common Trigger Failures

### 1. Description Too Vague

```yaml
# ❌ FAILS - No specific keywords
description: Helps with testing stuff.

# ✅ WORKS - Specific and keyword-rich
description: Write and run pytest tests. Use when creating tests, fixing test failures, or when user mentions "test", "pytest", "fixture", "mock".
```

### 2. Missing Keyword Variants

```yaml
# ❌ FAILS - Misses "tests" and "testing"
description: Write test code...

# ✅ WORKS - All variants
description: ...triggers on "test", "tests", "testing"...
```

### 3. No "Use When" Clause

```yaml
# ❌ FAILS - Claude doesn't know WHEN to use it
description: Handles test code.

# ✅ WORKS - Clear trigger conditions
description: ...Use when writing tests, reviewing test code, or debugging test failures.
```

### 4. Missing User Phrases

```yaml
# ❌ FAILS - Formal terms only
description: Implements unit test suites.

# ✅ WORKS - Includes casual phrases
description: ...triggers on "add tests", "write tests", "test this", "TDD this mf"...
```

## Frontmatter Fields Reference

### Triggering-Related

| Field | Effect on Triggering |
|-------|---------------------|
| `name` | Used with `/skill-name` invocation |
| `description` | **ONLY field that affects auto-triggering** |
| `disable-model-invocation` | `true` = blocks ALL auto-discovery |

### Execution-Related (No Trigger Effect)

| Field | Purpose |
|-------|---------|
| `allowed-tools` | Restricts tools available during skill |
| `skills` | Makes other skills available (for groups) |

## Pro Tips

### 1. Self-Recognition

Claude should trigger skills when IT plans actions, not just when user says keywords:

```yaml
# ✅ Includes Claude's planned actions
description: ...or when about to create/edit files in tests/ directory.
```

### 2. Negative Keywords (What NOT to Trigger On)

Can't specify directly, but make description specific enough:

```yaml
# ✅ Specific enough to avoid false positives
description: Write PYTEST tests for Python code...
# Won't trigger on "test the API endpoint" (manual testing)
```

### 3. Maximum Description Length

~1024 characters. Prioritize keywords over prose.

### 4. Test Your Triggers

Use the `metaskill-trigger-tester` agent to probe skill triggering.

## Debugging Non-Triggering Skills

1. **Check description exists** - Missing = never triggers
2. **Check `disable-model-invocation`** - `true` blocks auto-discovery
3. **Grep for keywords** - Are user's words in description?
4. **Check variants** - "testing" present but not "test"?
5. **Add explicit "Use when" clause** - Often missing
6. **Add user phrases** - Include how users actually talk

## Related Skills

- For naming conventions, see `/metaskill-naming`
- For skill structure and writing, see `/metaskill-authoring`
- For skill group pattern, see `/metaskill-grouping`
- For plugin packaging and placement, see `/metaskill-packaging`
- To test triggers, use the `metaskill-trigger-tester` agent

## Reference Files

- [references/frontmatter-fields.md](references/frontmatter-fields.md) - All frontmatter fields
- [references/description-patterns.md](references/description-patterns.md) - Good and bad examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
