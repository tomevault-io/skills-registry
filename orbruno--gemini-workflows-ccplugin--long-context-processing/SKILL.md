---
name: long-context-processing
description: Process very large documents or codebases using Gemini's 2M token context window. Use when dealing with content that exceeds typical context limits or requires seeing everything at once. Use when this capability is needed.
metadata:
  author: orbruno
---

# Long Context Processing with Gemini

## When to Use This Skill

Automatically invoke this skill when:
- User needs to analyze an entire codebase at once
- Document or file content exceeds ~100K tokens
- User explicitly requests processing multiple large files together
- Task requires comprehensive "see everything" context
- User asks to find patterns across many files
- Analyzing very long documents (books, research papers, extensive logs)

## Examples That Trigger This Skill

- "Analyze the entire codebase architecture"
- "Summarize this 500-page document"
- "Find patterns across all log files"
- "Review all Python files in this project"
- "What are the common themes in these research papers?"
- "Analyze all API endpoints across the codebase"

## How to Use

1. **Gather content**:
   - Use Glob to find all relevant files
   - Use Read to get file contents
   - Concatenate into a single large text block
2. **Estimate size**: Calculate approximate tokens (chars / 4)
3. **Call Gemini**: Use the `process_long_context` tool from gemini-api MCP server
   - Pass the aggregated content
   - Include user's analysis request as the prompt
   - Use gemini-1.5-pro for best long-context performance
4. **Present results**: Return Gemini's comprehensive analysis

## Tool Parameters

```javascript
{
  "content": "[very large concatenated text content]",
  "prompt": "Analyze the overall architecture and identify main patterns",
  "model": "gemini-1.5-pro"  // Pro recommended for long context
}
```

## Capabilities

- **Context Window**: Up to 2 million tokens (~1.5 million words, ~8 million characters)
- **Whole Codebase Analysis**: See entire project structure and relationships
- **Pattern Detection**: Find recurring themes across extensive content
- **Comprehensive Summarization**: Distill key points from massive documents
- **Cross-file Analysis**: Understand how different parts relate to each other

## When NOT to Use

- Small documents (< 50K tokens) - use Claude directly instead
- When detailed code editing is needed - Claude is better for precise changes
- Real-time or interactive tasks - prefer Claude's faster response
- When summarization is not the primary goal

## Best Practices

- Always use gemini-1.5-pro model for long context tasks
- Structure the prompt clearly: "Given the following [codebase/document/logs], [task]"
- Include file paths or section markers in content for better reference
- Ask for structured output (headings, lists) for easier parsing
- For very large content, consider if you truly need ALL of it or can filter first

## Content Formatting

When aggregating multiple files, use clear delimiters:

```
=== FILE: path/to/file1.py ===
[file content]

=== FILE: path/to/file2.py ===
[file content]
```

This helps Gemini maintain file context in its analysis.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/orbruno) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
