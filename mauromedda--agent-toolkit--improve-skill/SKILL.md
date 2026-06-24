---
name: improve-skill
description: >- Use when this capability is needed.
metadata:
  author: mauromedda
---

# ABOUTME: Skill for analyzing Claude Code sessions to improve or create skills
# ABOUTME: Extracts session transcripts and provides structured improvement workflows

# Improve Skill

Analyze Claude Code session transcripts to enhance existing skills or create new ones.

## When to Use This Skill vs Others

### Use improve-skill When:
- You've just finished a debugging/automation session and want to analyze it
- You need to **extract session transcript** for offline review
- You want to identify **what went wrong** in your workflow
- You're creating **new skills** from successful workflows
- You want to **improve existing skills** based on what you learned
- **Decision**: Use AFTER you've completed a web-automation session

### Use web-automation When:
- You're actively debugging or automating something NOW
- **Decision**: Use BEFORE you would use improve-skill (this is the "doing" skill)

### Typical Workflow

```
1. Run web-automation to debug/automate
   (this is the "doing" phase)

2. Save session transcript

3. Use improve-skill to analyze what happened
   (this is the "learning" phase)

4. Update skill documentation based on findings

5. Next time, your updated skill helps you work faster
```

## 🔄 RESUMED SESSION CHECKPOINT

**When a session is resumed from context compaction, verify skill improvement state:**

```
┌─────────────────────────────────────────────────────────────┐
│  SESSION RESUMED - IMPROVE-SKILL VERIFICATION               │
│                                                             │
│  Before continuing skill improvement work:                  │
│                                                             │
│  1. Was I in the middle of session analysis?                │
│     → Check /tmp/ for extracted transcripts                 │
│     → Check summary for "analyzing", "extracting"           │
│                                                             │
│  2. Was I updating a skill?                                 │
│     → Check which skill was being modified                  │
│     → Run: uv run validate_skill.py on the skill            │
│                                                             │
│  3. Did the previous work complete?                         │
│     → Check todo list for pending analysis tasks            │
│     → Verify skill validation passed                        │
│                                                             │
│  If analysis was in progress:                               │
│  → Re-run the extraction/analysis scripts                   │
│  → Do NOT assume previous analysis is still valid           │
└─────────────────────────────────────────────────────────────┘
```

## Quick Reference

| Script | Purpose |
|--------|---------|
| `extract_session.py` | Extract session transcript to markdown |
| `analyze_session.py` | Analyze session for skill improvements |

Run any script with `--help` for full options:
```bash
uv run ~/.claude/skills/improve-skill/scripts/extract_session.py --help
```

## Workflows

### 1. Improve an Existing Skill

When a skill didn't work as expected, extract the session and analyze it:

```bash
# Step 1: Extract the session transcript
uv run ~/.claude/skills/improve-skill/scripts/extract_session.py \
    --session-id <session-id> \
    --output /tmp/session.md

# Step 2: Analyze for improvements
uv run ~/.claude/skills/improve-skill/scripts/analyze_session.py \
    --transcript /tmp/session.md \
    --skill ~/.claude/skills/<skill-name>/SKILL.md \
    --output /tmp/analysis.md
```

Then start a **new Claude Code session** with:
```
Review the analysis in /tmp/analysis.md and update the skill accordingly.
```

**Why a new session?** Starting fresh ensures only the transcript and skill are loaded,
without prior context influencing the analysis.

### 2. Create a New Skill from Session

When you've done something successfully and want to capture it as a skill:

```bash
# Step 1: Extract the session
uv run ~/.claude/skills/improve-skill/scripts/extract_session.py \
    --session-id <session-id> \
    --output /tmp/session.md

# Step 2: Generate skill creation prompt
uv run ~/.claude/skills/improve-skill/scripts/analyze_session.py \
    --transcript /tmp/session.md \
    --create-skill \
    --output /tmp/new_skill_prompt.md
```

Then start a **new session** to create the skill based on the analysis.

### 3. Quick Session Review

List recent sessions and extract the most recent one:

```bash
# List recent sessions
uv run ~/.claude/skills/improve-skill/scripts/extract_session.py --list

# Extract most recent session for current directory
uv run ~/.claude/skills/improve-skill/scripts/extract_session.py --latest

# Extract most recent session for a specific project
uv run ~/.claude/skills/improve-skill/scripts/extract_session.py \
    --latest \
    --cwd /path/to/project
```

## Finding Session IDs

Session IDs can be found:
1. In the Claude Code UI (shown in session info)
2. Using the `/tasks` command
3. By listing sessions: `uv run ~/.claude/skills/improve-skill/scripts/extract_session.py --list`

## Analysis Framework

When analyzing a session transcript, look for:

| Pattern | What to Look For |
|---------|------------------|
| **Confusion** | Where did the agent struggle or misunderstand? |
| **Missing Info** | What information was needed but not in the skill? |
| **Workarounds** | What manual steps were needed that could be automated? |
| **Errors** | What errors occurred and how were they resolved? |
| **Success** | What approaches worked well and should be documented? |
| **Wrong Skill** | Was a different skill more appropriate? |

## Output Format

The analysis produces structured recommendations:

```markdown
## Session Analysis

### What Worked Well
- [List of successful approaches]

### Issues Identified
- [List of problems encountered]

### Recommended Improvements
- [Specific changes to make to the skill]

### Missing Documentation
- [Information that should be added]
```

## Help Output Examples

### extract_session.py

```
usage: extract_session.py [-h] [--list] [--latest] [--session-id SESSION_ID]
                          [--cwd CWD] [--output OUTPUT]

Extract Claude Code session transcripts to markdown

options:
  -h, --help            show this help message and exit
  --list                List all available sessions
  --latest              Extract most recent session
  --session-id SESSION_ID, -s SESSION_ID
                        Extract specific session by ID
  --cwd CWD             Filter sessions by working directory
  --output OUTPUT, -o OUTPUT
                        Save transcript to file (default: stdout)

Examples:
  uv run extract_session.py --list
  uv run extract_session.py --latest --output /tmp/session.md
  uv run extract_session.py --session-id abc123 --output /tmp/session.md
```

### analyze_session.py

```
usage: analyze_session.py [-h] --transcript TRANSCRIPT [--skill SKILL]
                          [--create-skill] [--quick] [--output OUTPUT]

Analyze Claude Code session transcripts for skill improvement

options:
  -h, --help            show this help message and exit
  --transcript TRANSCRIPT, -t TRANSCRIPT
                        Path to the session transcript markdown file (required)
  --skill SKILL, -s SKILL
                        Path to existing skill SKILL.md to improve
  --create-skill        Generate prompt for creating a new skill
  --quick               Quick analysis summary only (no full prompt)
  --output OUTPUT, -o OUTPUT
                        Output file path (default: stdout)

Examples:
  # Quick analysis
  uv run analyze_session.py --transcript /tmp/session.md

  # Improve existing skill
  uv run analyze_session.py --transcript /tmp/session.md \
      --skill ~/.claude/skills/web-automation/SKILL.md

  # Create new skill
  uv run analyze_session.py --transcript /tmp/session.md --create-skill

  # Save analysis to file
  uv run analyze_session.py --transcript /tmp/session.md --output /tmp/analysis.md
```

Output includes:
- **Automatically detected struggles** (missing files, permission issues, import errors, etc.)
- **Missing information** (implicit knowledge, missing examples, edge cases, etc.)
- **Success patterns** (what worked well, effective approaches)
- **Session metrics** (tool invocations, errors, retries, successes)
- **Assessment** (overall session quality and recommendations)

## Tips

- **Use a fresh session** for analysis to avoid context pollution
- **Focus on patterns**, not individual instances
- **Keep skills concise** - only add essential information
- **Include examples** for complex workflows
- **Update trigger words** if the skill wasn't invoked when expected
- **Start with quick analysis** to get overview, then full analysis for details

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mauromedda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
