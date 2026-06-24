---
name: prompt-library-minimal
description: Library of minimal prompts that use fewest tokens while delivering good results, with templates and best practices Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Prompt Library Minimal

## Skill Profile

- [ ] DevOps
- [ ] Backend
- [ ] Frontend
- [x] AI-RAG
- [ ] Security Critical

## Overview

Library of optimized prompts that use minimal tokens while maintaining effectiveness - suitable for production environments that need to reduce costs.

## Why This Matters

- **Cost savings**: Reduce token usage 50-70%
- **Speed**: Fewer tokens = faster responses
- **Proven**: Tested and production-ready
- **Reusable**: Copy-paste ready

## Core Concepts & Rules

### 1. Code Review

#### Verbose (❌ 85 tokens)

```
I would like you to please review this code carefully and provide
detailed feedback on potential bugs, performance issues, security
vulnerabilities, and code quality concerns. Please also suggest
improvements and best practices that could be applied.
```

#### Minimal (✅ 12 tokens)

```
Review for bugs, performance, security. Suggest improvements.
```

**Savings: 86%**

### 2. Bug Fix

#### Verbose (❌ 120 tokens)

```
I'm experiencing an issue where the login function is not working
properly. When users try to log in, they receive an error message.
Could you please help me identify what might be causing this problem
and suggest a solution? Here is the relevant code...
```

#### Minimal (✅ 18 tokens)

```
Login fails with error. Fix:
[code]

Expected: successful login
Actual: error message
```

**Savings: 85%**

### 3. Code Generation

#### Verbose (❌ 95 tokens)

```
Please write a function that will calculate the sum of two numbers.
The function should accept two parameters and return their sum.
Please include proper error handling and add comments explaining
what the code does.
```

#### Minimal (✅ 15 tokens)

```
Function: sum two numbers
Include: error handling, comments
Language: TypeScript
```

**Savings: 84%**

### 4. Refactoring

#### Verbose (❌ 110 tokens)

```
I have this code that works but I think it could be improved.
Could you please refactor it to make it more readable, maintainable,
and efficient? Please follow best practices and modern coding standards.
Also, please explain the changes you make.
```

#### Minimal (✅ 8 tokens)

```
Refactor for readability, efficiency:
[code]
```

**Savings: 93%**

### 5. Documentation

#### Verbose (❌ 75 tokens)

```
Please write comprehensive documentation for this function including
a description of what it does, parameters it accepts, what it
returns, and provide usage examples.
```

#### Minimal (✅ 6 tokens)

```
Document function:
[code]
```

**Savings: 92%**

### 6. Testing

#### Verbose (❌ 90 tokens)

```
I need you to write unit tests for this function. The tests should
cover normal cases, edge cases, and error cases. Please use Jest
as the testing framework and follow testing best practices.
```

#### Minimal (✅ 10 tokens)

```
Jest tests (normal, edge, error):
[code]
```

**Savings: 89%**

### 7. Debugging

#### Verbose (❌ 100 tokens)

```
I'm getting an error in my code and I can't figure out what's wrong.
The error message says "Cannot read property 'name' of undefined".
Can you help me understand what's causing this and how to fix it?
```

#### Minimal (✅ 12 tokens)

```
Error: Cannot read property 'name' of undefined
Code: [snippet]
Fix?
```

**Savings: 88%**

### 8. Optimization

#### Verbose (❌ 85 tokens)

```
This code is running slowly and I need to optimize it for better
performance. Can you analyze it and suggest ways to make it faster?
Please focus on algorithmic improvements and best practices.
```

#### Minimal (✅ 8 tokens)

```
Optimize for speed:
[code]
```

**Savings: 91%**

## Inputs / Outputs / Contracts

### Inputs

- Task descriptions
- Code snippets
- Error messages
- Requirements
- Constraints

### Outputs

- Minimal prompts
- Token-efficient templates
- Cost savings
- Quality maintained
- Reusable patterns

### Contracts

- **Input Validation**: All inputs must be valid task descriptions
- **Output Format**: Prompts follow minimal template standards
- **Token Budget**: Prompts respect configured token limits
- **Quality Guarantee**: Minimal prompts maintain effectiveness
- **Template Reusability**: Templates are copy-paste ready

## Skill Composition
* **Depends on**: [anti-bloat-checklist](../anti-bloat-checklist/SKILL.md), [context-pack-format](../context-pack-format/SKILL.md)
* **Compatible with**: [retrieval-playbook-for-ai](../retrieval-playbook-for-ai/SKILL.md), [skill-generator](../../00-meta-skills/skill-generator/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [summarization-rules-evidence-first](../summarization-rules-evidence-first/SKILL.md), [prompting-patterns](../../61-ai-production/prompting-patterns/SKILL.md)

## Quick Start

### Quick Reference Table

| Task | Minimal Prompt | Tokens |
|------|---------------|--------|
| Code review | `Review for bugs, performance, security:` | 4 |
| Bug fix | `Fix: [error]` | 2 |
| Generate | `Function: [description]` | 2 |
| Refactor | `Refactor for [goal]:` | 3 |
| Document | `Document:` | 1 |
| Test | `Tests ([cases]):` | 2 |
| Debug | `Error: [message]. Fix?` | 3 |
| Optimize | `Optimize for [metric]:` | 3 |

## Assumptions

- AI models understand concise prompts
- Token cost is a consideration
- Quality must be maintained
- Templates are reusable
- Production environment requires efficiency

## Compatibility

- **AI Models**: GPT-4, Claude, etc.
- **Languages**: All programming languages
- **Task Types**: Code review, bug fix, generation, etc.
- **Output Formats**: Code, text, JSON

## Test Scenario Matrix

| Scenario | Input | Expected Output | Verification |
|----------|-------|-----------------|--------------|
| Code review | Code snippet | Minimal review prompt | Token count reduced |
| Bug fix | Error message | Minimal fix prompt | Token count reduced |
| Generate | Function spec | Minimal generation prompt | Token count reduced |

## Technical Guardrails

### Prompt Requirements

- All prompts MUST use imperative mood
- All prompts MUST avoid filler words
- All prompts MUST specify output format
- All prompts MUST be task-specific

### Template Requirements

- All templates MUST be reusable
- All templates MUST use placeholders
- All templates MUST specify constraints
- All templates MUST be minimal

### Quality Requirements

- All prompts MUST maintain quality
- All prompts MUST be clear
- All prompts MUST be specific
- All prompts MUST be actionable

## Security Threat Model

### Threats Addressed

- **Token waste**: Minimal prompts reduce waste
- **Cost overruns**: Efficient prompts control costs
- **Quality degradation**: Tested prompts maintain quality
- **Ambiguity**: Clear, specific prompts

### Mitigation Strategies

- Use tested templates
- Monitor prompt effectiveness
- Track token usage
- Validate output quality
- Iterate on templates

## Domain-Specific Modules

### Prompt Template Module

```typescript
export interface PromptTemplate {
  task: string;
  template: string;
  tokens: number;
  savings: number;
}

export const PROMPT_TEMPLATES: PromptTemplate[] = [
  {
    task: "code-review",
    template: "Review for bugs, performance, security. Suggest improvements.",
    tokens: 12,
    savings: 86,
  },
  {
    task: "bug-fix",
    template: "Fix: {error}\nCode: {code}",
    tokens: 10,
    savings: 85,
  },
];
```

### Token Analyzer Module

```typescript
export function countTokens(text: string): number {
  // Approximate token count (4 chars per token)
  return Math.ceil(text.length / 4);
}

export function calculateSavings(before: number, after: number): number {
  return ((before - after) / before) * 100;
}
```

### Prompt Generator Module

```typescript
export function generatePrompt(
  task: string,
  details: Record<string, string>
): string {
  const template = PROMPT_TEMPLATES.find(t => t.task === task);
  if (!template) {
    throw new Error(`No template for task: ${task}`);
  }

  let prompt = template.template;
  for (const [key, value] of Object.entries(details)) {
    prompt = prompt.replace(`{${key}}`, value);
  }

  return prompt;
}
```

## Release, Rollback & Ops Notes

### Release Process

1. Define prompt templates
2. Test with AI models
3. Measure token savings
4. Validate output quality
5. Deploy to production
6. Monitor usage
7. Iterate on templates

### Rollback Procedure

1. Revert prompt changes
2. Restore previous templates
3. Monitor quality
4. Roll back if quality degrades

### Operational Procedures

- **Template management**: Maintain library of templates
- **Token monitoring**: Track usage and savings
- **Quality checks**: Validate output quality
- **Template updates**: Improve based on feedback

## Code Quality & Documentation

### Prompt Standards

- Use imperative mood
- Avoid filler words
- Be specific and clear
- Specify output format
- Include constraints

### Documentation Requirements

- Document all templates
- Provide examples
- Track token savings
- Include best practices
- Document usage patterns

## Agent Directives & Error Recovery

### Agent Behavior Rules

1. **Always** use imperative mood
2. **Always** avoid filler words
3. **Always** specify output format
4. **Always** be specific and clear
5. **Always** use minimal tokens

### Error Recovery Patterns

| Error Type | Detection | Recovery |
|------------|-----------|----------|
| Ambiguous prompt | Poor AI response | Add specificity |
| Too verbose | High token count | Apply template |
| Quality issue | Poor output | Add necessary context |

## Agent Prompt Pack

### Prompt Creation Prompts

```
"Create a minimal prompt for {task} that:
- Uses imperative mood
- Avoids filler words
- Specifies output format
- Is under 20 tokens
- Maintains quality"
```

### Template Optimization Prompts

```
"Optimize this prompt for minimal tokens:
- Remove filler words
- Use imperative mood
- Be specific
- Specify format
- Maintain quality"
```

### Prompt Evaluation Prompts

```
"Evaluate this prompt for {criteria}:
- Count tokens
- Check for filler words
- Verify imperative mood
- Assess clarity
- Suggest improvements"
```

## Definition of Done

Prompt optimization is complete when:

- [ ] All prompts use imperative mood
- [ ] All prompts avoid filler words
- [ ] All prompts specify output format
- [ ] All prompts are task-specific
- [ ] All prompts are minimal
- [ ] Token savings measured
- [ ] Quality maintained
- [ ] Templates documented
- [ ] Best practices followed

## Anti-patterns

1. **Verbose instructions**: Long, wordy prompts
2. **Filler words**: Unnecessary words
3. **Polite requests**: "Could you please", "I would like"
4. **No constraints**: Unlimited output
5. **Vague requests**: "Make it better"
6. **Multiple requests**: Repeating instructions
7. **No format spec**: Unclear output format
8. **Over-explaining**: Explaining what's obvious

## Reference Links

- [OpenAI Prompt Engineering](https://platform.openai.com/docs/guides/prompt-engineering)
- [Anthropic Prompt Library](https://docs.anthropic.com/claude/prompt-library)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)

## Versioning & Changelog

### v1.0.0 (2025-02-15)
- Initial release of Prompt Library Minimal skill
- Code review templates
- Bug fix templates
- Code generation templates
- Refactoring templates
- Documentation templates
- Testing templates
- Debugging templates
- Optimization templates
- Template library
- Output format specifications
- Constraints
- Best practices
- Quick reference
- Measurement and tracking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
