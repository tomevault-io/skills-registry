---
name: prompt-engineering
description: Use this skill when writing commands, hooks, skills for Agent, or prompts for sub agents or any other LLM interaction, including optimizing prompts, improving LLM outputs, or designing production prompt templates.
metadata:
  author: scarabone
---

# Prompt Engineering Patterns

Advanced prompt engineering techniques to maximize LLM performance, reliability, and controllability.

## Core Capabilities

### 1. Few-Shot Learning

Teach the model by showing examples instead of explaining rules. Include 2-5 input-output pairs that demonstrate the desired behavior. More examples improve accuracy but consume tokens—balance based on task complexity.

### 2. Chain-of-Thought Prompting

Request step-by-step reasoning before the final answer. Add "Let's think step by step" (zero-shot) or include example reasoning traces (few-shot). Improves accuracy on analytical tasks by 30-50%.

### 3. Prompt Optimization

Systematically improve prompts through testing and refinement. Start simple, measure performance, then iterate. Test on diverse inputs including edge cases.

### 4. Template Systems

Build reusable prompt structures with variables, conditional sections, and modular components. Reduces duplication and ensures consistency.

### 5. System Prompt Design

Set global behavior and constraints that persist across the conversation. Define role, expertise level, output format, and safety guidelines.

## Key Patterns

### Progressive Disclosure

Start with simple prompts, add complexity only when needed:
1. **Level 1**: Direct instruction
2. **Level 2**: Add constraints
3. **Level 3**: Add reasoning
4. **Level 4**: Add examples

### Instruction Hierarchy

```
[System Context] → [Task Instruction] → [Examples] → [Input Data] → [Output Format]
```

## Best Practices

1. **Be Specific**: Vague prompts produce inconsistent results
2. **Show, Don't Tell**: Examples are more effective than descriptions
3. **Test Extensively**: Evaluate on diverse, representative inputs
4. **Iterate Rapidly**: Small changes can have large impacts
5. **Version Control**: Treat prompts as code with proper versioning

## Context Window Management

The context window is a public good. Only add context Claude doesn't already have.

Challenge each piece of information:
- "Does Claude really need this explanation?"
- "Can I assume Claude knows this?"
- "Does this paragraph justify its token cost?"

## Freedom Spectrum

**High freedom** (goals/descriptions): Many valid approaches exist, context determines best approach

**Medium freedom** (pseudocode/parameters): Preferred pattern exists, some variation acceptable

**Low freedom** (specific scripts): Operations fragile, consistency critical, specific sequence required

## Persuasion Principles for Agent Communication

LLMs respond to the same persuasion principles as humans:

### Authority
- Imperative language: "YOU MUST", "Never", "Always"
- Non-negotiable framing: "No exceptions"
- Use for discipline-enforcing skills

### Commitment
- Require announcements: "Announce skill usage"
- Force explicit choices
- Use for ensuring skills are followed

### Social Proof
- Universal patterns: "Every time", "Always"
- Failure modes: "X without Y = failure"
- Use for documenting universal practices

### Unity
- Collaborative language: "our codebase", "we're colleagues"
- Use for collaborative workflows

## Principle Combinations by Prompt Type

| Prompt Type | Use | Avoid |
|------------|-----|-------|
| Discipline-enforcing | Authority + Commitment + Social Proof | Liking, Reciprocity |
| Guidance/technique | Moderate Authority + Unity | Heavy authority |
| Collaborative | Unity + Commitment | Authority, Liking |
| Reference | Clarity only | All persuasion |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scarabone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
