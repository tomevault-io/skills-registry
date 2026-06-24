---
name: agent-zero-comparison
description: Detailed product/option comparisons using Agent Zero Use when this capability is needed.
metadata:
  author: jason-easyazz
---
# Agent Zero Comparison

## When to Use
User wants to compare two or more options, products, or approaches.

## API Endpoints

### Submit Comparison
POST http://agent-zero-bridge:8101/tools/task
```json
{"task": "Compare X vs Y", "context": "comparison context"}
```

## Behavior
1. Identify the items being compared
2. Delegate to Agent Zero for thorough analysis
3. Present in a structured pros/cons or comparison format
4. End with a recommendation based on user's stated needs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-easyazz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
