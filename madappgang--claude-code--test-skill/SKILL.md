---
name: test-skill
description: Use when working with a test skill for validation testing. Use when testing skill parsing and validation logic.
metadata:
  author: madappgang
---

# Test Skill

This skill provides test functionality for validation testing.

## Prerequisites

Before using this skill:
1. Ensure `basic-skill` has been invoked
2. Verify `external-tool` is installed

## When to Use

Use this skill when:
- Testing skill parsing logic
- Validating frontmatter extraction
- Integration testing plugin loading

## Instructions

### Phase 1: Setup

Prepare the test environment:
1. Create necessary test files
2. Configure test parameters

### Phase 2: Execute

Run the test operation:
1. Invoke the required tools
2. Validate results

### Phase 3: Cleanup

Clean up after testing:
1. Remove temporary files
2. Reset state

## Example

```markdown
User: "Run the test skill"

Skill invokes Task tool with:
- subagent_type: "test-agent"
- description: "Execute test operation"
- prompt: "Perform test validation"
```

## Success Criteria

The skill succeeds when:
- All test operations complete
- No validation errors occur
- Results match expected output

---

**Test Skill v1.0.0**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madappgang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
