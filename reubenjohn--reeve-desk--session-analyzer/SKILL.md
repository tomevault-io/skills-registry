---
name: session-analyzer
description: Analyze Claude Code sessions for metrics and retrospection. Parses JSONL session files to extract message counts, tool usage, token consumption, and user feedback patterns. Used by daily-retrospection for budget tracking. Use when this capability is needed.
metadata:
  author: reubenjohn
---

# Session Analyzer

Analyze Claude Code sessions to extract metrics for retrospection and budget tracking.

## When to Use

- During daily/weekly retrospection to understand session costs
- To analyze specific sessions for debugging or review
- To track patterns in tool usage, token consumption, and user feedback
- For budget monitoring and cost optimization

## Scripts

All scripts are located in this directory (`.claude/skills/session-analyzer/`):

| Script | Purpose |
|--------|---------|
| [query_pulses.sh](query_pulses.sh) | Query today's completed pulses from SQLite |
| [get_todays_sessions.py](get_todays_sessions.py) | List sessions modified today |
| [analyze_session.py](analyze_session.py) | Full session analysis with metrics |
| [todays_summary.py](todays_summary.py) | Quick summary of today's sessions |
| [full_session_analysis.py](full_session_analysis.py) | Compact one-line analysis |
| [bulk_analysis.py](bulk_analysis.py) | Analyze all today's sessions |
| [skill_invocation_analysis.py](skill_invocation_analysis.py) | Count skill invocations across sessions |

## Data Sources

### 1. Pulse Queue Database

Location: `~/.reeve/pulse_queue.db`

Query completed pulses:
```bash
./query_pulses.sh
```

**Note:** The `session_id` column in pulses table may be empty for some pulses. The actual session files are created by Claude Code independently.

### 2. Sessions Index

Location: `~/.claude/projects/[YOUR_PROJECT_HASH]/sessions-index.json`

Structure:
```json
{
  "version": 1,
  "entries": [
    {
      "sessionId": "uuid-here",
      "fullPath": "~/.claude/projects/[YOUR_PROJECT_HASH]/uuid.jsonl",
      "fileMtime": 1769190637807,
      "firstPrompt": "The initial prompt...",
      "summary": "Auto-generated summary",
      "messageCount": 4,
      "created": "2026-01-23T06:46:56.260Z",
      "modified": "2026-01-23T06:47:06.575Z",
      "gitBranch": "master",
      "projectPath": "[YOUR_DESK_PATH]",
      "isSidechain": false
    }
  ]
}
```

### 3. Session JSONL Files

Location: `~/.claude/projects/[YOUR_PROJECT_HASH]/{sessionId}.jsonl`

Each line is a JSON object. Key types:
- `"type": "user"` - User message
- `"type": "assistant"` - Assistant response (contains usage data)
- `"type": "progress"` - Progress events (hooks, etc.)
- `"type": "summary"` - Session summary

## Workflow

### Step 1: Get Today's Sessions

```bash
cat ~/.claude/projects/[YOUR_PROJECT_HASH]/sessions-index.json | python3 get_todays_sessions.py
```

### Step 2: Analyze a Session File

```bash
cat ~/.claude/projects/[YOUR_PROJECT_HASH]/{SESSION_ID}.jsonl | python3 analyze_session.py
```

### Step 3: Generate Summary Report

After analyzing sessions, format the output:

```markdown
## Session Analysis: {session_id}
- Messages: X (user: Y, assistant: Z)
- Tool calls: N
- Input tokens: ~K (includes cache)
- Output tokens: M
- Duration: Xms
- Feedback signals: [list any detected]
```

## Quick One-Liner: Today's Sessions Summary

```bash
cat ~/.claude/projects/[YOUR_PROJECT_HASH]/sessions-index.json | python3 todays_summary.py
```

## Quick One-Liner: Full Session Analysis

```bash
cat ~/.claude/projects/[YOUR_PROJECT_HASH]/${SESSION_ID}.jsonl | python3 full_session_analysis.py
```

## Bulk Analysis: All Today's Sessions

```bash
cat ~/.claude/projects/[YOUR_PROJECT_HASH]/sessions-index.json | python3 bulk_analysis.py
```

## Skill Invocation Analysis

```bash
python3 skill_invocation_analysis.py
```

## Token Cost Estimation

Rough pricing (check current rates):
- Claude Opus: ~$15/M input, ~$75/M output
- Claude Sonnet: ~$3/M input, ~$15/M output
- Cache reads are discounted ~90%

Formula for daily cost estimate:
```python
# Approximate cost calculation
input_cost = (input_tokens / 1_000_000) * 15  # Opus rate
output_cost = (output_tokens / 1_000_000) * 75
total_cost = input_cost + output_cost
print(f"Estimated cost: ${total_cost:.2f}")
```

## Feedback Pattern Detection

The analyzer looks for these signals that may indicate corrections or issues:

| Pattern | Interpretation |
|---------|---------------|
| "no," / "no " | Direct rejection |
| "wrong" | Error identified |
| "actually" | Correction incoming |
| "fix" / "error" | Problem found |
| "don't" / "stop" | Unwanted action |
| "should be" / "instead" | Correction |
| "undo" / "cancel" | Reversal needed |

High feedback signal counts may indicate:
- Misunderstanding user intent
- Errors in execution
- Need for better prompting
- Learning opportunities

## Integration with Daily Retrospection

Use this skill in `/daily-retrospection` to:
1. Get list of today's completed pulses
2. For each pulse with a session, analyze the JSONL
3. Aggregate totals (messages, tools, tokens, feedback)
4. Calculate estimated cost
5. Log insights to Diary/

Example output for retrospection:
```markdown
## Daily Session Metrics - 2026-02-05

| Metric | Value |
|--------|-------|
| Sessions | 8 |
| Total messages | 142 |
| Tool calls | 87 |
| Input tokens | ~1.2M |
| Output tokens | ~45K |
| Est. cost | $21.38 |
| Feedback signals | 3 |

Notable sessions:
- c431664c (87 msgs): Complex skill creation, 11 tool calls
- 33f86f7c (26 msgs): Morning briefing, standard flow

Feedback detected:
- [fix] in session abc123: "fix the formatting..."
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reubenjohn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
