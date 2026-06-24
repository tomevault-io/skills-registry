---
name: before-code-analysis
description: Use BEFORE reading multiple files to understand code. Reminds you to use codebase-analyzer agent instead of manual file reading. Use when this capability is needed.
metadata:
  author: eveld
---

# Before Code Analysis

**STOP**: You're about to read multiple files to understand how something works.

## Use codebase-analyzer Instead

The `codebase-analyzer` agent is specifically designed for understanding code implementation. It will read files in context and explain how systems work.

### Instead of:
```
Read(file_path="src/auth/handler.js")
Read(file_path="src/auth/service.js")
Read(file_path="src/auth/middleware.js")
[manually piecing together how auth works]
```

### Do this:
```
Task(
  subagent_type="codebase-analyzer",
  prompt="Analyze how the authentication system works, including handlers, services, and middleware",
  description="Analyze auth system"
)
```

## When Manual Reading Is OK

Only read files manually when:
- You need to see exact implementation of a single function
- You're making specific edits to known files
- You're verifying a specific detail

For understanding systems or flows, use `codebase-analyzer`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eveld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
