---
name: test-restricted-tools
description: Test if allowed-tools actually restricts available tools Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Test Tool Restriction

**Goal**: Verify if `allowed-tools: Write` restricts access to other tools.

## Task

1. Try to use the Write tool (should work)
2. Try to use the Read tool (should be blocked?)
3. Try to use Bash tool (should be blocked?)
4. Report which tools are available vs blocked

Write your findings to: `earnings-analysis/test-outputs/restricted-tools-result.txt`

Include:
- List of tools you can see/access
- Which tools worked vs were blocked
- Whether allowed-tools restriction is enforced

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
