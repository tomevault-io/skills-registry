---
name: codex-consult
description: This skill should be used when the user asks to "consult Codex", "ask Codex for advice", "get implementation suggestions from Codex", "codexに相談", "codexにきいて", "実装方針を相談", "アーキテクチャをcodexに聞く", or needs deeper analysis using OpenAI's Codex model for complex implementation decisions, architecture design, or code review that benefits from extended reasoning. Use when this capability is needed.
metadata:
  author: masa10-f
---

# Codex Consult

## Overview

This skill enables consultation with OpenAI's Codex CLI for deeper implementation analysis. Codex uses more powerful reasoning models (gpt-5.2-codex) that can provide thorough architectural guidance and implementation plans.

**When to use:**
- Complex architectural decisions requiring deep analysis
- Implementation planning for multi-file changes
- Code review needing extended reasoning
- Design pattern recommendations
- Refactoring strategy development

## Execution Modes

### Plan Mode (Default)
Provides analysis and implementation planning without code changes.

Output includes:
1. Summary of the approach
2. Implementation strategy
3. Verification steps

### Patch Mode
Provides analysis with concrete code change suggestions in unified diff format.

Output includes:
1. Summary of proposed changes
2. Unified diff patches
3. Verification steps

## Arguments

| Argument | Description | Default |
|----------|-------------|---------|
| Task description | Required. What to analyze or plan | - |
| `--model=<model>` | Model to use | gpt-5.2-codex |
| `--mode=plan\|patch` | Execution mode | plan |
| `--scope=<path>` | Target directory | current repo root |
| `--no-web` | Disable web search | web enabled by default |

## Usage Instructions

### Basic Consultation (Plan Mode)

To consult Codex for implementation planning:

1. Execute the wrapper script with task description:
   ```bash
   bash ${CLAUDE_PLUGIN_ROOT}/scripts/codex-exec.sh "Implement user authentication with JWT"
   ```

2. Read the output from `/tmp/codex-consult.last.md`

3. If output is empty or error occurs, check `/tmp/codex-consult.run.log`

### Patch Mode Consultation

To get concrete code change suggestions:

```bash
bash ${CLAUDE_PLUGIN_ROOT}/scripts/codex-exec.sh --mode=patch "Refactor the database connection to use connection pooling"
```

### Custom Model

To use a different model:

```bash
bash ${CLAUDE_PLUGIN_ROOT}/scripts/codex-exec.sh --model=o4-mini "Design the caching layer"
```

### Scoped Analysis

To analyze a specific directory:

```bash
bash ${CLAUDE_PLUGIN_ROOT}/scripts/codex-exec.sh --scope=/path/to/src "Review error handling patterns"
```

### Disable Web Search

To run without web search:

```bash
bash ${CLAUDE_PLUGIN_ROOT}/scripts/codex-exec.sh --no-web "Analyze this legacy code"
```

## Output Interpretation

### Summary Section
- What changes are proposed
- Assumptions and preconditions
- Impact analysis

### Changes Section (Patch Mode)
- Unified diff format
- File-by-file breakdown
- Explanation of each change

### Verification Section
- Test commands to run
- Manual verification steps
- Edge cases to check

## Best Practices

1. **Be specific**: Provide clear task descriptions with context
2. **Always specify `--scope` explicitly**: Use `--scope=$(pwd)` or `--scope=/absolute/path/to/project` to ensure Codex analyzes the correct project context. This is especially important in multi-repository environments.
3. **Start with plan mode**: Use plan mode first, then patch mode if needed
4. **Verify suggestions**: Always review Codex output before applying changes
5. **Consider execution time**: Complex analyses may take longer. Avoid running as background tasks with strict timeouts.

## Caveats

### Scope Resolution in Multi-Repository Environments

When working in directories that contain multiple git repositories (e.g., nested subprojects or monorepos), Codex may analyze the wrong project context if `--scope` is not explicitly specified.

**Problem scenario:**
```
/home/user/projects/
├── main-project/
│   └── submodules/
│       └── other-repo/      ← Codex may analyze this instead
└── .git/
```

**Solution:** Always use explicit `--scope` parameter:
```bash
# From any directory, specify the target project explicitly
bash ${CLAUDE_PLUGIN_ROOT}/scripts/codex-exec.sh --scope=/home/user/projects/main-project "Analyze the codebase"

# Or use $(pwd) to ensure current directory is used
bash ${CLAUDE_PLUGIN_ROOT}/scripts/codex-exec.sh --scope=$(pwd) "Analyze the codebase"
```

### Timeout Considerations

Codex CLI may encounter timeout failures (exit code 144) in the following scenarios:

1. **Background task execution**: Running as a background process with strict timeout limits
2. **Complex analysis requests**: Large codebases or architecturally complex queries requiring extended reasoning
3. **Network latency**: Slow connections can cause API timeouts

**Recommendations:**
- **Set timeout to at least 10 minutes (600 seconds)**: Complex analyses require extended processing time. Shorter timeouts will likely cause failures.
- Run Codex consultations in the foreground when possible
- Break down complex queries into smaller, focused questions
- For large codebases, use `--scope` to limit the analysis scope
- Check `/tmp/codex-consult.run.log` for timeout-related errors

## Error Handling

If Codex execution fails:
1. Check `/tmp/codex-consult.run.log` for error details
2. Verify Codex CLI is authenticated (`codex login`)
3. Check network connectivity
4. Try with a different model if quota exceeded

## Additional Resources

### Reference Files
- **`references/codex-cli-reference.md`** - Codex CLI commands and options
- **`references/output-format.md`** - Output format specification

### Examples
- **`examples/sample-consultation.md`** - Sample consultation outputs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masa10-f) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
