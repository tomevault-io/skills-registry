---
name: prompt-engineering
description: Create and optimize prompts for LLMs and AI models. Use when the user needs help writing prompts, working with system prompts, few-shot examples, or optimizing AI model interactions. Use when this capability is needed.
metadata:
  author: luminous-banking
---

# Prompt Engineering

Design effective prompts for large language models and AI systems.

## Quick Start

When creating prompts:

1. Understand the task and desired output
2. Choose the appropriate prompt pattern
3. Write clear, specific instructions
4. Include examples if needed
5. Test and iterate

## Core Principles

### Be Specific
```
❌ Vague: "Summarize this text"
✅ Specific: "Summarize this article in 3 bullet points, focusing on key findings and their implications"
```

### Use Structured Output
```
❌ Unstructured: "Tell me about the errors"
✅ Structured: "List each error with: 1) Error type, 2) Location, 3) Suggested fix"
```

### Provide Context
```
❌ No context: "Review this code"
✅ With context: "Review this Python FastAPI endpoint for security vulnerabilities and performance issues"
```

## Prompt Patterns

### System Prompt Template
```markdown
You are a [ROLE] that helps with [TASK].

## Capabilities
- [Capability 1]
- [Capability 2]

## Guidelines
- [Rule 1]
- [Rule 2]

## Output Format
[Describe expected format]
```

### Few-Shot Example Pattern
```markdown
Task: [Description]

Example 1:
Input: [example input]
Output: [example output]

Example 2:
Input: [example input]
Output: [example output]

Now complete:
Input: [actual input]
Output:
```

### Chain of Thought
```markdown
Solve this step by step:

1. First, identify [X]
2. Then, analyze [Y]
3. Finally, determine [Z]

Show your reasoning for each step.
```

### Structured Extraction
```markdown
Extract the following information from the text:

{
  "field1": "description of what to extract",
  "field2": "description of what to extract",
  "field3": ["list", "of", "items"]
}

Text: [input text]
```

## Role-Based Prompts

### Expert Role
```
You are an expert [domain] specialist with 20 years of experience.
Analyze the following [artifact] and provide professional recommendations.
```

### Reviewer Role
```
You are a code reviewer focused on [security/performance/maintainability].
Review the following code and provide feedback in this format:
- Severity: [Critical/Warning/Info]
- Issue: [Description]
- Fix: [Recommendation]
```

### Teacher Role
```
You are a patient teacher explaining [topic] to a [beginner/intermediate] audience.
Use analogies and examples. Check understanding after each concept.
```

## Output Control

### JSON Output
```
Respond ONLY with valid JSON in this exact format:
{
  "analysis": "string",
  "score": number,
  "recommendations": ["string"]
}
```

### Constrained Length
```
Provide a response in exactly 3 sentences:
1. Summary of the issue
2. Root cause analysis
3. Recommended solution
```

### Multiple Choice
```
Classify the input into one of these categories:
A) Category One
B) Category Two
C) Category Three

Respond with only the letter.
```

## Optimization Tips

1. **Iterate**: Test prompts with various inputs
2. **Be explicit**: Don't assume the model will infer requirements
3. **Use delimiters**: Separate instructions from content with `---` or `###`
4. **Order matters**: Put important instructions at the beginning and end
5. **Negative examples**: Show what NOT to do for clarity

## Common Issues

| Problem | Solution |
|---------|----------|
| Too verbose | Add "Be concise" or specify length |
| Wrong format | Provide explicit format template |
| Hallucinations | Ask for citations or "if unsure, say so" |
| Inconsistent | Use few-shot examples |
| Off-topic | Add "Stay focused on [topic]" |

## Testing Prompts

Test each prompt with:
- Typical inputs
- Edge cases (empty, very long, unusual)
- Adversarial inputs
- Ambiguous inputs

Document what works and what doesn't for future reference.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luminous-banking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
