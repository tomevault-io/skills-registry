---
name: ffuf-claude-skill
description: Web fuzzing with ffuf Use when this capability is needed.
metadata:
  author: techwavedev
---

# Ffuf Claude Skill

## Overview

Web fuzzing with ffuf

## When to Use This Skill

Use this skill when you need to work with web fuzzing with ffuf.

## Instructions

This skill provides guidance and patterns for web fuzzing with ffuf.

For more information, see the [source repository](https://github.com/jthack/ffuf_claude_skill).

---

<!-- AGI-INTEGRATION-START -->

## AGI Framework Integration

> **Adapted for [@techwavedev/agi-agent-kit](https://www.npmjs.com/package/@techwavedev/agi-agent-kit)**
> Original source: [antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills)

### Memory-First Protocol

Retrieve prior decisions and patterns to avoid re-discovering solutions. Cache results for instant retrieval in future sessions.

```bash
# Check for prior development context before starting
python3 execution/memory_manager.py auto --query "prior work and patterns related to Ffuf Claude Skill"
```

### Storing Results

After completing work, store development decisions for future sessions:

```bash
python3 execution/memory_manager.py store \
  --content "Completed task with key insights documented for future reference" \
  --type decision --project <project> \
  --tags ffuf-claude-skill default
```

### Multi-Agent Collaboration

Share outcomes with other agents so the team stays aligned and avoids duplicate work.

```bash
python3 execution/cross_agent_context.py store \
  --agent "<your-agent>" \
  --action "Task completed — results documented and shared with team" \
  --project <project>
```

<!-- AGI-INTEGRATION-END -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/techwavedev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
