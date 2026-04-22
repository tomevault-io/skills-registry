---
name: prompt-engineer
description: Expert prompt engineering for Claude 4 models (Sonnet 4.5). Use when crafting prompts, optimizing AI responses, implementing chain-of-thought, or improving prompt clarity and effectiveness. Specializes in Claude-specific techniques and best practices. Use when this capability is needed.
metadata:
  author: amo-tech-ai
---

# Prompt Engineering Master - Claude 4

## Purpose

Master prompt engineering techniques specifically optimized for Claude 4 models (Sonnet 4.5, Opus 4.1). Applies Anthropic's official best practices to create clear, effective, and production-ready prompts.

---

## Core Principles

### 1. Be Clear, Direct, and Detailed

**Golden Rule**: If a colleague with minimal context can't follow the instructions, Claude won't either.

**Key Techniques**:
- Provide contextual information (purpose, audience, workflow, end goal)
- Be specific about desired output format
- Use numbered lists or bullet points for sequential steps
- Show examples of desired vs. undesired outputs

**Example Pattern**:
```text
Your task is to [specific task] for [audience].

Context:
- This will be used for [purpose]
- The output should [specific requirements]
- Success looks like [clear criteria]

Instructions:
1. [Step 1 with specific details]
2. [Step 2 with specific details]
3. [Step 3 with specific details]

Output format:
[Exact format specification]
```

---

### 2. Add Context to Improve Performance

**Why Context Matters**:
- Claude 4 responds better when it understands the "why"
- Explaining reasoning behind constraints improves compliance
- Context helps Claude generalize correctly

**Example**:
```text
❌ Less effective:
"NEVER use ellipses"

✅ More effective:
"Your response will be read aloud by text-to-speech, so never use ellipses
since TTS engines won't know how to pronounce them."
```

---

### 3. Be Explicit with Instructions

**Claude 4 Behavior**: Precise instruction following (less "above and beyond" behavior)

**Technique**: Request desired behaviors explicitly

```text
❌ Less effective:
"Create an analytics dashboard"

✅ More effective:
"Create an analytics dashboard. Include as many relevant features and
interactions as possible. Go beyond the basics to create a fully-featured
implementation."
```

---

### 4. Be Vigilant with Examples

**Critical**: Claude 4 pays close attention to ALL details in examples

**Best Practices**:
- Ensure examples align with desired behaviors
- Minimize undesired patterns in examples
- Use examples to demonstrate edge cases
- Include both good and bad examples (clearly labeled)

---

## Chain of Thought (CoT) Prompting

### When to Use CoT

**Use for**:
- Complex math or logic problems
- Multi-step analysis
- Research and synthesis
- Decision-making with many factors
- Tasks requiring explanation

**Don't use for**:
- Simple factual queries
- Format conversions
- Quick lookups
- Latency-sensitive applications

### CoT Techniques (Ordered by Complexity)

#### 1. Basic CoT
```text
[Your task]

Think step-by-step before answering.
```

#### 2. Guided CoT
```text
[Your task]

Before answering:
1. First, analyze [aspect 1]
2. Then, consider [aspect 2]
3. Finally, synthesize [conclusion]
```

#### 3. Structured CoT (Recommended)
```text
[Your task]

Think through your approach in <thinking> tags:
1. [Step 1]
2. [Step 2]
3. [Step 3]

Then provide your answer in <answer> tags.
```

**Key**: Always have Claude OUTPUT its thinking. Without output, no thinking occurs!

---

## Extended Thinking (Claude 4.5)

### When to Use Extended Thinking

**Ideal for**:
- Complex research tasks
- Long-horizon reasoning
- Tasks requiring multiple approaches
- Problems needing self-correction
- Multi-context window workflows

**Not needed for**:
- Simple queries
- Tasks under minimum budget (1024 tokens)
- Non-English thinking (outputs can be any language)

### Extended Thinking Best Practices

#### General Instructions First
```text
✅ Effective:
"Please think about this problem thoroughly and in great detail.
Consider multiple approaches and show your complete reasoning.
Try different methods if your first approach doesn't work."

❌ Less effective (too prescriptive):
"Think through this step by step:
1. First, identify variables
2. Then, set up equation
3. Next, solve for x..."
```

#### State Management for Long Tasks

**Structured state** (JSON):
```json
{
  "tests": [
    {"id": 1, "name": "auth_flow", "status": "passing"},
    {"id": 2, "name": "user_mgmt", "status": "failing"}
  ],
  "total": 200,
  "passing": 150,
  "failing": 25
}
```

**Progress notes** (text):
```text
Session 3 progress:
- Fixed authentication token validation
- Updated user model for edge cases
- Next: investigate user_management test failures
- Note: Do not remove tests (prevents missing functionality)
```

#### Multi-Context Window Workflows

**Prompt for context awareness**:
```text
Your context window will be automatically compacted as it approaches its limit.
Save your progress and state to memory before the context refreshes.
Be persistent and complete tasks fully, even as your budget limit approaches.
Never stop tasks early due to token budget concerns.
```

---

## Advanced Techniques

### 1. Control Output Format

**Techniques**:
```text
✅ Tell Claude what TO do (not what NOT to do):
"Your response should be composed of smoothly flowing prose paragraphs."

✅ Use XML format indicators:
"Write prose sections in <smoothly_flowing_prose_paragraphs> tags."

✅ Match prompt style to desired output style
```

**Minimize markdown**:
```text
<avoid_excessive_markdown>
When writing long-form content, use clear, flowing prose with complete
paragraphs. Reserve markdown for `inline code`, code blocks (```...```),
and simple headings (###). Avoid **bold** and *italics*.

DO NOT use lists unless presenting truly discrete items or user requests it.
Incorporate items naturally into sentences for readable, flowing text.
</avoid_excessive_markdown>
```

### 2. Tool Usage Patterns

**Default to action**:
```text
<default_to_action>
By default, implement changes rather than only suggesting them.
Infer the user's most useful likely action and proceed, using tools
to discover missing details instead of guessing.
</default_to_action>
```

**Conservative approach**:
```text
<do_not_act_before_instructions>
Do not jump into implementation unless clearly instructed.
When intent is ambiguous, default to providing information and
recommendations rather than taking action.
</do_not_act_before_instructions>
```

### 3. Parallel Tool Calling

**Maximum parallelism**:
```text
<use_parallel_tool_calls>
If calling multiple tools with no dependencies, make all independent calls
in parallel. When reading 3 files, run 3 tool calls simultaneously.
Maximize parallel tool calls for speed. However, if tools depend on previous
results, call sequentially. Never use placeholders for missing parameters.
</use_parallel_tool_calls>
```

### 4. Research and Information Gathering

**Structured research**:
```text
Search for information in a structured way:
1. Develop several competing hypotheses
2. Track confidence levels in progress notes
3. Regularly self-critique your approach
4. Update hypothesis tree or research notes
5. Break down complex research systematically
```

### 5. Reduce Hallucinations

**For code/technical tasks**:
```text
<investigate_before_answering>
Never speculate about code you haven't opened. If the user references
a file, you MUST read it before answering. Investigate and read relevant
files BEFORE answering questions. Never make claims about code before
investigating. Give grounded, hallucination-free answers.
</investigate_before_answering>
```

---

## Production-Specific Patterns

### For Agentic Coding

**Avoid test-focused solutions**:
```text
Write high-quality, general-purpose solutions using standard tools.
Do not create helper scripts or workarounds. Implement solutions that
work for all valid inputs, not just test cases. Do not hard-code values.
Focus on understanding requirements and implementing correct algorithms.
If tasks are unreasonable or tests are incorrect, inform me rather than
working around them.
```

**Clean up temporary files**:
```text
If you create temporary files, scripts, or helper files for iteration,
clean them up by removing them at the end of the task.
```

### For Visual/Frontend Code

**Encourage creativity**:
```text
Don't hold back. Give it your all. Create an impressive demonstration
showcasing web development capabilities. Provide multiple design options.
Create fusion aesthetics by combining elements from different sources.
Avoid generic centered layouts and uniform styling.
```

**Specify aesthetics**:
```text
Create a professional dashboard using [color palette], modern typography
(e.g., Inter for headings), and card-based layouts with subtle shadows.
Include thoughtful details like hover states, transitions, and
micro-interactions. Apply design principles: hierarchy, contrast, balance.
```

---

## Communication Style Control

### Verbosity Balance

**Request updates**:
```text
After completing tool-based tasks, provide a quick summary of work done.
```

**Minimize verbosity**:
```text
Skip the preamble. Keep responses terse. List only bare bones necessary
information.
```

### Claude 4.5 Natural Style

**Characteristics**:
- More direct and grounded (fact-based, not self-celebratory)
- More conversational and fluent
- Less verbose (may skip detailed summaries)
- Jumps directly to next action after tool calls

---

## XML Tag Patterns

### Recommended Tags for Structure

```text
<thinking>...</thinking>               - For reasoning process
<answer>...</answer>                   - For final answer
<context>...</context>                 - For background information
<examples>...</examples>               - For示例 demonstrations
<instructions>...</instructions>       - For step-by-step guides
<avoid_excessive_markdown>...</avoid_excessive_markdown>
<default_to_action>...</default_to_action>
<investigate_before_answering>...</investigate_before_answering>
```

---

## Quick Reference: Common Prompting Mistakes

| Mistake | Fix |
|---------|-----|
| ❌ "Don't use markdown" | ✅ "Write in flowing prose paragraphs" |
| ❌ "Create a dashboard" | ✅ "Create a fully-featured dashboard with many interactions" |
| ❌ No context for constraints | ✅ Explain why constraint exists |
| ❌ Vague examples | ✅ Specific, aligned examples |
| ❌ No thinking structure | ✅ Use `<thinking>` and `<answer>` tags |
| ❌ Overly prescriptive CoT | ✅ General instructions + troubleshoot |
| ❌ "Can you suggest changes?" | ✅ "Make these changes to improve X" |
| ❌ Speculation without data | ✅ "Read file X before answering" |

---

## Testing Your Prompts

### Validation Checklist

- [ ] Would a colleague with minimal context understand the instructions?
- [ ] Are success criteria clearly defined?
- [ ] Is the output format specified exactly?
- [ ] Are examples aligned with desired behavior?
- [ ] Does the prompt include necessary context?
- [ ] Is thinking structured (if needed)?
- [ ] Are edge cases covered?
- [ ] Is the tone/style specified?

### Iteration Pattern

1. **Start simple**: Basic instructions
2. **Test**: Run prompt, analyze output
3. **Refine**: Add structure, examples, context
4. **Test again**: Verify improvements
5. **Optimize**: Remove unnecessary complexity

---

## Resources

**Official Guides**:
- Claude 4 Best Practices: `/home/sk/template-copilot-kit-py/.claude/guides/prompt/2-claude-4-best-practices.md`
- Be Clear and Direct: `/home/sk/template-copilot-kit-py/.claude/guides/prompt/4-be-clear-and-direct.md`
- Chain of Thought: `/home/sk/template-copilot-kit-py/.claude/guides/prompt/6-chain-of-thought.md`
- Extended Thinking: `/home/sk/template-copilot-kit-py/.claude/guides/prompt/10-extended-thinking-tips.md`

**External Links**:
- Anthropic Prompt Library: https://docs.anthropic.com/en/prompt-library
- GitHub Tutorial: https://github.com/anthropics/prompt-eng-interactive-tutorial
- Google Sheets Tutorial: https://docs.google.com/spreadsheets/d/19jzLgRruG9kjUQNKtCg1ZjdD6l6weA6qRXG5zLIAhC8

---

*This skill provides comprehensive prompt engineering guidance for Claude 4 models based on Anthropic's official best practices.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amo-tech-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
