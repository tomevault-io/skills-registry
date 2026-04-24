---
name: libanalysis
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libanalysis Skill

## When to Use

- Preparing documents for LLM-based code review
- Formatting markdown with line numbers for reference
- Creating numbered documents for analysis tasks
- Pre-processing content before sending to AI

## Key Concepts

**formatForAnalysis**: Formats markdown content using Prettier and prepends line
numbers, making it easy for LLMs to reference specific lines in their responses.

## Usage Patterns

### Pattern 1: Format document for analysis

```javascript
import { formatForAnalysis } from "@copilot-ld/libanalysis";

const document = `# README
This is a code sample.`;

const formatted = await formatForAnalysis(document);
// Returns:
// 1: # README
// 2: This is a code sample.
```

## Integration

Standalone utility used in analysis scripts and evaluation pipelines.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
