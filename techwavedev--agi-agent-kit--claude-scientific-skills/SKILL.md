---
name: claude-scientific-skills
description: Scientific research and analysis skills Use when this capability is needed.
metadata:
  author: techwavedev
---

# Claude Scientific Skills

## Overview

Scientific research and analysis skills

## When to Use This Skill

Use this skill when you need to work with scientific research and analysis skills.

## Instructions

This skill provides guidance and patterns for scientific research and analysis skills.

For more information, see the [source repository](https://github.com/K-Dense-AI/claude-scientific-skills).

---

<!-- AGI-INTEGRATION-START -->

## AGI Framework Integration

> **Adapted for [@techwavedev/agi-agent-kit](https://www.npmjs.com/package/@techwavedev/agi-agent-kit)**
> Original source: [antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills)

### Memory-First Protocol

Retrieve prior documentation structure and content to maintain consistency. Cache generated docs to avoid regenerating unchanged sections.

```bash
# Check for prior documentation context before starting
python3 execution/memory_manager.py auto --query "documentation patterns and prior content for Claude Scientific Skills"
```

### Storing Results

After completing work, store documentation decisions for future sessions:

```bash
python3 execution/memory_manager.py store \
  --content "Documentation: API reference generated from OpenAPI spec, deployment guide updated with new env vars" \
  --type technical --project <project> \
  --tags claude-scientific-skills documentation
```

### Multi-Agent Collaboration

Share documentation changes with all agents so they reference the latest guides and APIs.

```bash
python3 execution/cross_agent_context.py store \
  --agent "<your-agent>" \
  --action "Documentation updated — API reference, deployment guide, and CHANGELOG all current" \
  --project <project>
```

### Agent Team: Documentation

This skill pairs with `documentation_team` — dispatched automatically after any code change to keep docs in sync.

<!-- AGI-INTEGRATION-END -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/techwavedev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
