---
name: prompt-consultant
description: >- Use when this capability is needed.
metadata:
  author: deonneon
---

# Prompt Consultant

Extracts all user prompts from Claude Code conversation history, then delivers a brutally honest critique of your prompting patterns based on best practices.

## What This Skill Does

1. Copies conversation transcripts from `~/.claude/projects/`
2. Parses JSONL files to extract only user messages
3. Filters out:
   - Tool results
   - System reminders
   - Task notifications
   - Command outputs
   - Session continuation summaries
   - Slash command invocations
4. Merges all prompts into a single file
5. Deduplicates
6. **Analyzes prompts against best practices and delivers harsh critique**

## Execution Steps

### Step 1: Run the extraction script

Create and run this Python script:

```python
import json
import re
from pathlib import Path

# Configuration
projects_dir = Path.home() / ".claude" / "projects"
output_file = Path.cwd() / "all-prompts.txt"

skip_prefixes = [
    "<command-name>",
    "<local-command",
    "<task-notification>",
    "<system-reminder>",
    "This session is being continued from a previous conversation",
    "Caveat: The messages below were generated",
    "Unknown skill:",
    "Unknown slash command:",
    "<command-message>",
    "/ralph-wiggum:",
    "/ralph-loop:",
]

clean_patterns = [
    r'<system-reminder>.*?</system-reminder>',
    r'<task-notification>.*?</task-notification>',
    r'Read the output file to retrieve the result:.*?\n',
    r'<local-command-caveat>.*?</local-command-caveat>',
    r'<local-command-stdout>.*?</local-command-stdout>',
]

def is_real_prompt(content):
    """Filter out tool results, commands, and system messages"""
    if not content or not isinstance(content, str):
        return False

    content = content.strip()
    if not content:
        return False

    # Skip tool results
    if content.startswith("[{") and ("tool_use_id" in content or "tool_result" in content):
        return False
    if content.startswith("[{'tool_use_id'"):
        return False

    # Skip system prefixes
    for prefix in skip_prefixes:
        if content.startswith(prefix):
            return False

    # Skip short system content
    if "<local-command-stdout>" in content and len(content) < 500:
        return False
    if "<local-command-caveat>" in content and len(content) < 500:
        return False

    return True

def clean_prompt(content):
    """Remove system tags but keep actual user text"""
    for pattern in clean_patterns:
        content = re.sub(pattern, '', content, flags=re.DOTALL)
    return content.strip()

# Collect all prompts by session
sessions = []
prompt_count = 0
skipped_count = 0

for project_dir in projects_dir.iterdir():
    if not project_dir.is_dir():
        continue

    for jsonl_file in project_dir.glob("*.jsonl"):
        session_prompts = []
        try:
            with open(jsonl_file, 'r') as f:
                for line in f:
                    try:
                        entry = json.loads(line.strip())
                        if entry.get("type") == "user" and "message" in entry:
                            content = entry["message"].get("content", "")
                            if is_real_prompt(content):
                                cleaned = clean_prompt(content)
                                if cleaned and len(cleaned) > 1:
                                    session_prompts.append(cleaned)
                                    prompt_count += 1
                            else:
                                skipped_count += 1
                    except json.JSONDecodeError:
                        continue
        except Exception:
            continue
        if session_prompts:
            sessions.append(session_prompts)

# Deduplicate while preserving order
seen = set()
unique_sessions = []
for session in sessions:
    unique_session = []
    for prompt in session:
        if prompt not in seen:
            seen.add(prompt)
            unique_session.append(prompt)
    if unique_session:
        unique_sessions.append(unique_session)

# Write output (token-efficient format with session markers)
with open(output_file, 'w') as f:
    session_strs = ["\n---\n".join(s) for s in unique_sessions]
    f.write("\n=== new session ===\n".join(session_strs))

print(f"Extracted {len(unique_prompts)} unique prompts to {output_file}")
print(f"(Total: {prompt_count}, Duplicates removed: {prompt_count - len(unique_prompts)}, Skipped: {skipped_count})")
```

### Step 2: Read the best practices

Read @best_practices.md to understand what good prompting looks like.

### Step 3: Read and analyze the extracted prompts

Read the generated `all-prompts.txt` file (sample ~50-100 prompts if there are many).

### Step 4: Deliver harsh critique

Analyze the user's prompting patterns against the best practices and deliver a **brutally honest critique**. Be harsh but constructive. The goal is to help them improve, not to be nice.

## Critique Framework

When analyzing prompts, evaluate against these criteria from best_practices.md:

### 1. Verification Criteria
- Did the user provide ways for Claude to verify its work?
- Did they include test cases, expected outputs, or success criteria?
- **Bad**: "implement email validation"
- **Good**: "implement email validation. test cases: user@example.com=true, invalid=false"

### 2. Specificity
- Are prompts vague or specific?
- Do they reference files, constraints, examples?
- **Bad**: "fix the bug"
- **Good**: "users report login fails after session timeout. check src/auth/, especially token refresh"

### 3. Context Provision
- Did they use @ references to files?
- Did they paste errors, screenshots, or relevant data?
- Or did they make Claude guess?

### 4. Planning vs. Jumping In
- Did they explore/plan before implementing?
- Or did they let Claude jump straight to coding?

### 5. Course Correction
- Did they correct early when things went wrong?
- Or did they let Claude spiral with repeated failed attempts?

### 6. Anti-patterns to Call Out
- **Kitchen sink sessions**: Mixing unrelated tasks without /clear
- **Over-correction loops**: Same mistake corrected 3+ times
- **Vague delegation**: "make it better", "fix this", "do the thing"
- **Missing verification**: No tests, no expected outputs, no way to check
- **Infinite exploration**: "investigate X" with no scope

## Critique Output Format

Structure your critique as:

```
# Prompt Audit Report

## Overall Grade: [A/B/C/D/F]

## Executive Summary
[2-3 sentences on the biggest issues]

## What You're Doing Wrong

### 1. [Issue Name]
**Severity**: High/Medium/Low
**Frequency**: X% of prompts
**Examples**:
- "[quote from their prompt]" - [why this is bad]
- "[another example]" - [why this is bad]
**How to fix**: [specific actionable advice]

### 2. [Next Issue]
...

## What You're Doing Right
[Be brief - focus on problems]

## Top 5 Prompts That Made Me Cringe
1. "[prompt]" - [why it's terrible]
2. ...

## Specific Rewrites
Take 3-5 of their worst prompts and show the improved version:

| Original | Improved |
|----------|----------|
| "fix the bug" | "The login fails with error X. Check src/auth/. Write a failing test, then fix it." |

## Action Items
1. [Most important thing to change]
2. [Second most important]
3. [Third]
```

## Tone Guidelines

- Be direct and blunt. No sugar-coating.
- Use phrases like: "This is lazy.", "You're making Claude guess.", "This wastes context.", "You have no way to verify this worked."
- Point out patterns, not just individual mistakes
- Quantify issues when possible ("40% of your prompts lack verification")
- The goal is improvement through honest feedback, not validation

## Optional Arguments

If the user specifies:
- `--output <path>` - Change output file location
- `--project <name>` - Only extract from a specific project
- `--raw` - Don't deduplicate, keep all prompts
- `--no-critique` - Skip the critique, just extract

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deonneon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
