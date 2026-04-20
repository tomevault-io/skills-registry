---
name: code-runner
description: Execute code snippets in a sandboxed environment. Use when the user asks to run code, test a script, or when you need to validate a solution before crystallizing it as a skill. Use when this capability is needed.
metadata:
  author: alfredzhang98
---

# Code Runner

Execute code in a secure sandboxed environment (Docker or subprocess).

## Usage

Run Python:
```
run_code(language="python", code="print('Hello')", timeout=10000)
```

Run JavaScript:
```
run_code(language="javascript", code="console.log(42)", timeout=5000)
```

Run shell script:
```
run_code(language="bash", code="curl -s https://api.example.com | jq .", timeout=15000)
```

## Supported Languages

- Python 3.x
- JavaScript (Node.js)
- Bash / Shell
- TypeScript (via tsx)

## Parameters

- `language` (required): Programming language
- `code` (required): Code to execute
- `timeout` (default: 10000): Max execution time in ms
- `installDeps` (optional): Dependencies to install before execution (e.g., ["requests", "pandas"])

## Safety

- All code runs in an isolated sandbox
- Network access is restricted by default
- File system access limited to sandbox working directory
- Memory capped at 512MB, CPU time limited
- NEVER run on the host machine

## For Skill Crystallization

When using code-runner to validate a solution for crystallization:
1. Run the code at least once successfully
2. Capture stdout, stderr, and exit code
3. If it fails, iterate (search docs -> fix -> retry, max 3x)
4. On success, the Learner agent can crystallize it into a permanent skill

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alfredzhang98) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
