---
name: omni-audit
description: Pre-commit security audit using local Ollama models. Free, fast, and private code review before you commit. Use when this capability is needed.
metadata:
  author: quique1996
---

# Omni-Audit

Security and quality audit for your staged git changes using local Ollama models. Runs before commit to catch issues early.

## Usage

When the user invokes `/omni-audit`, run the audit on staged changes.

### Commands

**Full audit** (default - security + quality + edge cases):
```bash
python3 ~/.claude/skills/omni-audit/scripts/ollama-audit.py --full
```

**Security audit only**:
```bash
python3 ~/.claude/skills/omni-audit/scripts/ollama-audit.py --security
```

**Quality audit only**:
```bash
python3 ~/.claude/skills/omni-audit/scripts/ollama-audit.py --quality
```

**Edge cases audit**:
```bash
python3 ~/.claude/skills/omni-audit/scripts/ollama-audit.py --edge-cases
```

**Audit a specific diff file**:
```bash
python3 ~/.claude/skills/omni-audit/scripts/ollama-audit.py --diff /path/to/changes.diff
```

**Quiet mode** (just status):
```bash
python3 ~/.claude/skills/omni-audit/scripts/ollama-audit.py --quiet
```

## What It Checks

### Security Audit
- SQL injection vulnerabilities
- Command injection risks
- Hardcoded secrets or credentials
- XSS vulnerabilities
- Path traversal issues
- Unsafe deserialization

### Quality Audit
- Obvious bugs or logic errors
- Missing error handling
- Resource leaks (unclosed files, connections)
- Race conditions
- Off-by-one errors

### Edge Cases Audit
- Null/None/undefined handling
- Empty collections
- Boundary values (0, -1, MAX_INT)
- Unicode/encoding issues
- Timeout scenarios

## Interpreting Results

The audit returns one of:
- **PASS** - No issues found, safe to commit
- **WARN** - Minor concerns, review before committing
- **FAIL** - Critical issues found, fix before committing
- **SKIP** - Ollama unavailable or timeout

## Example Workflow

```
User: /omni-audit

1. Run: git diff --cached (to see what's staged)
2. Run: python3 ~/.claude/skills/omni-audit/scripts/ollama-audit.py --full
3. Report results to user
4. If FAIL: Suggest specific fixes
5. If PASS: Confirm safe to commit
```

## Pre-commit Hook Integration

Add to `.git/hooks/pre-commit`:
```bash
#!/bin/bash
python3 ~/.claude/skills/omni-audit/scripts/ollama-audit.py --security --quiet
exit $?
```

## Requirements

- Python 3.10+
- Git
- Ollama with `qwen2.5-coder:7b` (or configure different model)
- Staged git changes to audit

## Fallback

If Ollama is not running:
- Script exits with code 0 (doesn't block commit)
- Reports "SKIP: Ollama not available"
- Recommend: "Run `ollama serve` in another terminal"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/quique1996) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
