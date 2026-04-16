---
name: test-skill
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Test Skill

This is a minimal skill used for E2E testing of the Claude Code plugin system.

## Purpose

This skill validates:
- Skill loading from plugin directory
- Frontmatter parsing (YAML)
- Trigger phrase detection
- Tool permission validation
- 2025 schema compliance

## Activation

This skill activates when you use phrases like:
- "run test skill"
- "execute test"
- "test skill activation"

## Allowed Tools

- **Read** - Read files for validation
- **Write** - Write test results
- **Bash** - Execute test commands

## Implementation

This skill performs basic test operations to validate the plugin system works correctly.

### Test Operations

1. Read test files
2. Write test results
3. Execute validation commands

## Expected Behavior

When activated, this skill should:
- Load successfully from the plugin
- Parse frontmatter correctly
- Match trigger phrases
- Respect tool permissions

## Error Handling

If this skill fails to activate:
- Check plugin installation
- Verify SKILL.md frontmatter
- Validate allowed-tools format
- Ensure trigger phrases are correct

## Resources

- E2E Test Suite Documentation: `/tests/e2e/README.md`
- Plugin Structure: `/.claude-plugin/plugin.json`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
