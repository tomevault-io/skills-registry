---
name: prompt-engineering
description: > Use when this capability is needed.
metadata:
  author: ismailtrm
---

# Prompt Engineering

Techniques to maximize LLM performance, reliability, and controllability.

## Quick Reference

| Technique | When to Use | Reference |
|-----------|-------------|-----------|
| Few-Shot | Consistent formatting, classification | `references/core-techniques.md` |
| Chain-of-Thought | Multi-step reasoning, math, debugging | `references/core-techniques.md` |
| Structured Output | JSON/XML parsing, schema enforcement | `references/structured-outputs.md` |
| Tool Descriptions | Function calling, agent tools | `references/tool-use-prompting.md` |
| Extended Thinking | Complex analysis, deep reasoning | `references/claude-specific.md` |
| Prompt Caching | Repeated prompts, cost optimization | `references/claude-specific.md` |

## Core Principles

**1. Be Specific** — Vague prompts → inconsistent results

**2. Show, Don't Tell** — Examples outperform descriptions

**3. Structure Matters** — Use XML tags, clear delimiters, consistent formatting

**4. Start Simple** — Add complexity only when needed

**5. Test Broadly** — Diverse inputs including edge cases

## Instruction Hierarchy

```
[System Context] → [Task] → [Examples] → [Input] → [Output Format]
```

Put stable instructions in system prompts. Reserve user messages for variable content.

## Key Patterns

### Progressive Disclosure
1. Direct instruction
2. Add constraints  
3. Add reasoning steps
4. Add examples

### Error Recovery
Include: fallback instructions, confidence handling, missing info behavior.

```markdown
If information is missing: [specify fallback]
If uncertain: [specify behavior]
If task cannot be completed: [specify response]
```

## References

| File | Content |
|------|---------|
| `references/core-techniques.md` | Few-shot, chain-of-thought, optimization |
| `references/structured-outputs.md` | JSON, XML, schemas, parsing |
| `references/tool-use-prompting.md` | Tool descriptions, parameters, orchestration |
| `references/claude-specific.md` | Extended thinking, caching, artifacts |
| `references/agent-prompting.md` | Context window, degrees of freedom |
| `references/persuasion-principles.md` | Authority, commitment, compliance patterns |

## Templates

Reusable templates in `assets/templates/`:

| Template | Use Case |
|----------|----------|
| `system-prompt.md` | System prompt structure |
| `tool-description.md` | Tool/function definitions |
| `few-shot-classifier.md` | Classification with examples |
| `agent-task.md` | Agent/sub-agent task prompts |

## Common Pitfalls

- Over-engineering before trying simple prompts
- Example pollution (examples don't match target)
- Context overflow (too many examples)
- Ambiguous instructions without examples
- Missing error/edge case handling

## Performance Tips

**Token efficiency:**
- Remove redundant words
- Use abbreviations after first definition
- Move stable content to system prompts
- Use prompt caching for repeated prefixes

**Latency:**
- Minimize prompt length
- Use streaming for long outputs
- Cache common prompt prefixes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ismailtrm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
