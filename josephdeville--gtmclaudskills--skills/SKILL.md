---
name: continuous-learning
description: Automatically extract reusable patterns from Claude Code sessions and evolve them into learned skills, instincts, and custom instructions. Use when this capability is needed.
metadata:
  author: josephdeville
---

# Continuous Learning Skill

Automatically evaluate Claude Code sessions, extract reusable patterns, and evolve them into actionable knowledge that improves future interactions.

## Overview

This skill transforms your Claude Code sessions into a self-improving knowledge base. Rather than losing valuable insights when a session ends, it captures patterns, corrections, and solutions, then organizes them for future use.

## Architecture

```
Session Activity
      │
      ▼
┌─────────────────┐
│  Observation    │  ← Hooks capture all tool use and corrections
│     Layer       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Analysis      │  ← Background agent identifies patterns
│    Engine       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Instinct      │  ← Atomic behaviors with confidence scores
│    Store        │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Evolution     │  ← Cluster instincts into skills/instructions
│    Pipeline     │
└─────────────────┘
```

## How It Works

### Phase 1: Observation (Hooks)

Hooks provide 100% reliable observation of session activity:

| Hook Type | Captures |
|-----------|----------|
| `PreToolUse` | Intent before tool execution |
| `PostToolUse` | Results and any failures |
| `Stop` | Full session context at end |

### Phase 2: Pattern Detection

The system identifies these pattern types:

| Pattern | Description | Example |
|---------|-------------|---------|
| `error_resolution` | How specific errors were solved | "ECONNREFUSED on port 3000 → check if server running" |
| `user_corrections` | When user corrects Claude's approach | "Don't use semicolons in this codebase" |
| `workarounds` | Solutions for framework quirks | "Next.js 14: use 'use client' for useState" |
| `debugging_techniques` | Effective debugging methods | "Add console.log before async boundaries" |
| `project_conventions` | Project-specific patterns | "This repo uses kebab-case for file names" |
| `tool_preferences` | Preferred tools/approaches | "User prefers ripgrep over grep" |

### Phase 3: Instinct Creation

Patterns are stored as **instincts**: atomic behavioral units with metadata:

```json
{
  "id": "inst_a1b2c3",
  "trigger": "when encountering CORS error in fetch",
  "behavior": "check if backend has cors middleware enabled",
  "confidence": 0.7,
  "domain": ["debugging", "api"],
  "source_sessions": ["session_123", "session_456"],
  "created_at": "2025-02-10T14:30:00Z",
  "last_validated": "2025-02-11T09:15:00Z"
}
```

**Confidence Scoring:**
- Initial confidence: 0.5
- +0.1 when pattern repeats successfully
- +0.2 when user explicitly confirms
- -0.2 when behavior is corrected
- Instincts below 0.3 are archived

### Phase 4: Evolution Pipeline

High-confidence instincts (0.7+) evolve into higher-order constructs:

```
Instincts (0.7+)
      │
      ├──► Skill Files (3+ related instincts)
      │    └── ~/.claude/skills/learned/debugging-react.md
      │
      ├──► Custom Instructions (behavioral patterns)
      │    └── Added to CLAUDE.md or settings
      │
      └──► Agent Templates (complex workflows)
           └── Reusable multi-step procedures
```

## Configuration

Create `~/.claude/continuous-learning/config.json`:

```json
{
  "observation": {
    "hooks_enabled": true,
    "min_session_messages": 8,
    "capture_tool_failures": true,
    "capture_user_edits": true
  },
  "analysis": {
    "background_model": "haiku",
    "extraction_threshold": "medium",
    "domains": [
      "code-style",
      "testing",
      "git",
      "debugging",
      "api",
      "database",
      "deployment"
    ]
  },
  "instincts": {
    "initial_confidence": 0.5,
    "evolution_threshold": 0.7,
    "decay_rate": 0.05,
    "archive_threshold": 0.3,
    "max_instincts": 500
  },
  "evolution": {
    "auto_approve": false,
    "cluster_min_instincts": 3,
    "skill_output_path": "~/.claude/skills/learned/",
    "instruction_output_path": "~/.claude/instructions/"
  },
  "ignore_patterns": [
    "simple_typos",
    "one_time_fixes",
    "external_api_outages",
    "rate_limit_errors"
  ]
}
```

## Installation

### 1. Hook Configuration

Add to `~/.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/continuous-learning/observe.sh pre $TOOL_NAME"
      }]
    }],
    "PostToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/continuous-learning/observe.sh post $TOOL_NAME $EXIT_CODE"
      }]
    }],
    "Stop": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/continuous-learning/analyze-session.sh"
      }]
    }]
  }
}
```

### 2. Directory Structure

```
~/.claude/continuous-learning/
├── config.json
├── observe.sh
├── analyze-session.sh
├── instincts/
│   ├── active/
│   │   └── inst_*.json
│   └── archived/
├── sessions/
│   └── session_*.log
└── exports/
    └── instincts-export.json
```

### 3. Scripts Setup

**observe.sh** - Captures tool events:

```bash
#!/bin/bash
# Lightweight event capture - runs on every tool use
EVENT_TYPE=$1
TOOL_NAME=$2
EXIT_CODE=$3
TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

echo "{\"type\":\"$EVENT_TYPE\",\"tool\":\"$TOOL_NAME\",\"exit\":\"$EXIT_CODE\",\"ts\":\"$TIMESTAMP\"}" \
  >> "$HOME/.claude/continuous-learning/sessions/current.log"
```

**analyze-session.sh** - Runs at session end:

```bash
#!/bin/bash
# Full analysis at session end - can be heavier
SESSION_LOG="$HOME/.claude/continuous-learning/sessions/current.log"
CONFIG="$HOME/.claude/continuous-learning/config.json"

# Check minimum session length
MSG_COUNT=$(wc -l < "$SESSION_LOG")
MIN_MSGS=$(jq -r '.observation.min_session_messages' "$CONFIG")

if [ "$MSG_COUNT" -lt "$MIN_MSGS" ]; then
  exit 0
fi

# Trigger background analysis
# This calls Claude with Haiku to extract patterns
~/.claude/continuous-learning/extract-patterns.py "$SESSION_LOG" &
```

## Commands

### Manual Pattern Extraction

Use during a session to immediately capture a pattern:

```
/learn "pattern description"
```

Examples:
- `/learn "this project uses pnpm not npm"`
- `/learn "always run tests before committing"`
- `/learn "API responses need camelCase transformation"`

### View Learned Instincts

```
/instincts [domain]
```

Shows active instincts, optionally filtered by domain.

### Export/Import

Share instincts between machines or team members:

```
/instincts export > my-instincts.json
/instincts import < team-instincts.json
```

## Example Learned Patterns

### From Error Resolution

**Trigger:** Claude encounters "Module not found: Can't resolve '@/components'"

**Learned Instinct:**
```json
{
  "trigger": "Module not found with @ alias",
  "behavior": "Check tsconfig.json paths configuration and verify baseUrl is set correctly",
  "confidence": 0.85,
  "domain": ["debugging", "typescript"]
}
```

### From User Correction

**Session Event:** User says "No, use the existing logger, don't create a new one"

**Learned Instinct:**
```json
{
  "trigger": "Need logging functionality",
  "behavior": "Search for existing logger/logging utilities before creating new ones",
  "confidence": 0.9,
  "domain": ["code-style", "project_conventions"]
}
```

### Evolved into Skill

After 5+ related instincts about this project's conventions:

**~/.claude/skills/learned/acme-project-conventions.md:**
```markdown
# ACME Project Conventions

## Logging
- Use existing `src/utils/logger.ts` for all logging
- Never instantiate new console.log patterns

## Imports
- Use @ alias for src/ directory
- Prefer named exports over default

## Testing
- Co-locate tests with source files (*.test.ts)
- Use vitest, not jest
```

## Performance Considerations

| Component | Impact | Mitigation |
|-----------|--------|------------|
| PreToolUse hook | ~5ms per tool | Minimal logging only |
| PostToolUse hook | ~5ms per tool | Append-only file writes |
| Stop hook analysis | ~2-5s once | Background process, non-blocking |
| Instinct lookup | ~10ms | In-memory cache with domain indexing |

## Comparison: v1 vs v2

| Aspect | v1 (Original) | v2 (This Version) |
|--------|---------------|-------------------|
| Observation | Stop hook only (end of session) | Pre/Post hooks (100% coverage) |
| Granularity | Full skills | Atomic instincts that evolve |
| Confidence | None | 0.0-1.0 with decay/growth |
| Learning | Direct to skills | Instincts → Clusters → Skills |
| Sharing | Not supported | Export/Import instincts |
| Domains | Generic | Tagged by domain |
| Analysis | Main context | Background agent (Haiku) |

## Troubleshooting

**Instincts not being created:**
- Check `min_session_messages` threshold
- Verify hooks are configured in settings.json
- Check `~/.claude/continuous-learning/sessions/` for logs

**Too many low-quality instincts:**
- Raise `extraction_threshold` to "high"
- Add patterns to `ignore_patterns`
- Lower `initial_confidence` to require more validation

**Instincts not evolving to skills:**
- Check if confidence meets `evolution_threshold`
- Verify at least `cluster_min_instincts` related instincts exist
- Run `/instincts evolve` to manually trigger clustering

## References

- [Homunculus v2](https://github.com/humanplane/homunculus) - Inspiration for instinct-based architecture
- [Claude Code Hooks Documentation](https://docs.anthropic.com/claude-code/hooks)
- [Longform Guide - Continuous Learning](https://x.com/affaanmustafa/status/2014040193557471352)

---

*This skill continuously improves Claude's effectiveness by learning from every session. The more you use it, the better it understands your preferences, project conventions, and effective problem-solving patterns.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/josephdeville) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
