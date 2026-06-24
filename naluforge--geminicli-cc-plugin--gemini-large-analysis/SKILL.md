---
name: gemini-large-analysis
description: >- Use when this capability is needed.
metadata:
  author: NaluForge
---

# Large Codebase Analysis with Gemini

**Invoke using `/gemini-analyze` or `mcp__gemini__gemini_execute` with `include_directories` set to target paths.**

## When to Use Gemini vs Claude

Use Gemini when the analysis scope exceeds ~200K tokens — roughly a full
repository or multiple large modules. Claude can handle focused analysis of
individual files or small groups, but whole-repo audits, dependency trees,
and cross-cutting architectural questions benefit from Gemini's 1M-token
context window.

## Recommended Approach

### 1. Scope First, Then Send

Before calling `gemini_execute`, understand what you're sending:
- Use Glob to map the file tree: `**/*.ts`, `**/*.py`, etc.
- Use Grep to find specific patterns: entry points, exports, imports
- Estimate the token count (rough: 1 line ≈ 10 tokens)
- If scope exceeds even Gemini's context, plan multiple focused passes

### 2. Parameter Selection

- **model**: Always `pro` — codebase analysis requires deep reasoning
- **timeout_ms**: `2400000` (40 minutes) — full-repo analysis can take 20-40 minutes
- **include_directories**: Target specific directories rather than the entire
  repo when possible. This reduces noise and improves analysis quality.
- **max_turns**: Increase if you want Gemini to use tools (file reading, etc.)

### 3. Breaking Into Passes

For truly massive codebases, break into focused passes:
1. **Architecture pass**: "Map the high-level module structure and dependencies"
2. **Pattern pass**: "Identify design patterns and anti-patterns in src/"
3. **Quality pass**: "Find code quality issues, dead code, and tech debt in lib/"
4. **Synthesis**: Combine findings from each pass

### 4. Cross-Reference with Local Inspection

Gemini's findings are a starting point. For each finding:
- Verify with local file reads (the code may have changed)
- Check git blame for context on why code is the way it is
- Run relevant tests to confirm suspected issues

## Output Format

Structure analysis output as:
1. **Key findings** — the most important observations
2. **Architecture insights** — module structure, dependency flow, coupling
3. **Issues** — with file:line references and severity
4. **Recommendations** — concrete, actionable next steps

---
> Source: [NaluForge/geminicli-cc-plugin](https://github.com/NaluForge/geminicli-cc-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
