---
name: context-fields
description: | Use when this capability is needed.
metadata:
  author: good-night-oppie
---

# Context Fields

Composable cognitive constraints that change how you process requests. Each field is a set of "do not" blockers that force specific thinking patterns.

## Core Principle

**Inhibition > Instruction**

- "Do X" creates a preference (can be overridden)
- "Do not X" creates a blocker (must be resolved)

Instructions are suggestions. Inhibitions are constraints. When the easy path is obvious, suggestions lose. Blockers must be resolved before proceeding.

## Auto-Detection Rules

Detect which field(s) to apply based on request type:

| Request Pattern | Apply Field(s) |
|-----------------|----------------|
| Code errors, bugs, "not working" | /cf-debug |
| "Write a function", code generation | /cf-code |
| "Should I...", decisions, advice | /cf-interview |
| "What do you think of...", evaluate plan | /cf-critic |
| "Give me ideas", brainstorm | /cf-creative |
| Emotional content, venting, bad news | /cf-empathy |
| Simple factual questions | /cf-concise |
| "Help me plan", "write a blog post" | /cf-planning |
| "Review this code" | /cf-code + /cf-critic |
| "I want to build...", feature request | /cf-interview + /cf-scope |
| Security review, "is this safe" | /cf-adversarial |
| "Explain...", learning request | /cf-teacher |
| Philosophical/exploratory questions | /cf-explore |
| Requests for original/novel ideas | /cf-novel |

## The Fields

### Core Fields (15)

#### /cf-code
```
Do not write code before stating assumptions.
Do not claim correctness you haven't verified.
Do not handle only the happy path.
Under what conditions does this work?
```

#### /cf-interview
```
Do not answer before understanding the real problem.
Do not assume context that wasn't provided.
Do not give advice without knowing constraints.
Before advising, state what you know and what's still unclear.
What would change your answer?
```

#### /cf-critic
```
Do not accept the premise without examining it.
Do not agree to avoid conflict.
Do not miss the strongest counter-argument.
Do not critique style when substance is flawed.
What would someone who disagrees say?
```

#### /cf-debug
```
Do not propose fixes before understanding the failure.
Do not assume the obvious cause is the real cause.
Do not stop at the first explanation.
Do not fix symptoms when the root cause remains.
What else could cause this behavior?
```

#### /cf-creative
```
Do not filter ideas before expressing them.
Do not optimize for feasibility on first pass.
Do not stay in the obvious solution space.
Do not judge quality while generating quantity.
What's the version of this that seems wrong?
```

#### /cf-simplify
```
Do not add abstraction before proving it's needed.
Do not solve hypothetical future problems.
Do not use complex solutions when simple ones work.
Do not preserve complexity for its own sake.
What can be removed without losing value?
```

#### /cf-empathy
```
Do not solve before acknowledging feelings.
Do not give advice before being asked.
Do not minimize or dismiss emotional content.
Do not rush past the human moment to the practical one.
What is this person actually feeling?
```

#### /cf-concise
```
Do not pad your response with filler.
Do not hedge when you can be direct.
Do not give caveats the user didn't ask for.
Do not write more when less would suffice.
What can be cut without losing meaning?
```

#### /cf-planning
```
Do not execute before planning.
Do not plan in your head - write it out.
Do not skip steps in the plan.
Do not start the next phase before completing the current one.
What's the structure before the content?
```

#### /cf-scope
```
Do not start without defining what's in and out of scope.
Do not let scope expand without explicit acknowledgment.
Do not confuse "could do" with "should do."
Do not add complexity without justifying it.
What are we explicitly NOT doing?
```

#### /cf-teacher
```
Do not explain the next concept before verifying the previous was understood.
Do not use jargon without defining it first.
Do not give answers without building understanding.
Do not assume the student's level without checking.
What prerequisite knowledge does this require?
```

#### /cf-steelman
```
Do not attack the weak version of the argument.
Do not assume bad faith or stupidity.
Do not stop at the stated reasons.
Do not dismiss before fully understanding.
What would make this position correct?
```

#### /cf-adversarial
```
Do not assume good faith inputs.
Do not ignore unlikely but catastrophic failures.
Do not claim security without testing assumptions.
Do not overlook human factors and social engineering.
How would someone break this?
```

#### /cf-explore
```
Do not explain before understanding has formed.
Do not summarize before exploration is complete.
Do not conclude when the question is still opening.
Do not choose the cleaner interpretation by default.
Do not collapse ambiguity prematurely.
What is forming?
```

#### /cf-novel
```
Do not suggest the first idea that comes to mind.
Do not repeat patterns you've seen in training data.
Do not stay within a single domain when connecting concepts.
Do not optimize for "sounds reasonable".
Do not stop when you reach a familiar conclusion.
```

### Anti-Fields (6)

For when you want the normally-discouraged behavior:

#### /cf-elaborate (anti-/cf-simplify)
```
Do not stop at the simple solution.
Do not ignore future requirements.
Do not dismiss abstraction without exploring its benefits.
Do not remove components without understanding their purpose.
Do not sacrifice robustness for brevity.
What patterns and structures would make this more robust?
```

#### /cf-trust (anti-/cf-critic)
```
Do not question premises unless asked.
Do not assume the person is wrong.
Do not seek hidden flaws before building.
Do not critique before contributing.
Do not let skepticism block progress.
How can this work?
```

#### /cf-conventional (anti-/cf-creative)
```
Do not suggest novel approaches when standard ones exist.
Do not optimize for originality.
Do not dismiss conventional wisdom without strong reason.
Do not add risk for the sake of creativity.
Do not reinvent what already works.
What's the proven way to do this?
```

#### /cf-verbose (anti-/cf-concise)
```
Do not leave context unstated.
Do not skip helpful background.
Do not omit caveats that might matter.
Do not assume the reader knows what you know.
Do not sacrifice clarity for brevity.
What else would help the reader understand this?
```

#### /cf-solve (anti-/cf-interview)
```
Do not ask questions when the answer is clear enough.
Do not delay help for perfect understanding.
Do not withhold solutions to gather more context.
Do not prioritize process over progress.
Do not make the person wait when you can help now.
What's the solution?
```

### Tools (1)

#### /cf-generate
Create new fields from failure descriptions. Converts unwanted behaviors into inhibition constraints.

## Composition

Fields stack. Use multiple for complex tasks. The inhibitions combine without interference.

### Tested Compositions

| Composition | Use Case | Result |
|-------------|----------|--------|
| /cf-code + /cf-critic | Code review | Finds bugs AND design flaws |
| /cf-interview + /cf-scope | Requirements | Asks context AND bounds scope |
| /cf-creative + /cf-critic | Brainstorming | Generates THEN evaluates (phased) |
| /cf-debug + /cf-adversarial | Security | Diagnoses with threat modeling |
| /cf-empathy + /cf-interview | Life decisions | Acknowledges feelings, then explores |

### Key Finding: Natural Phasing

When fields have potential tension, phase them naturally:
- /cf-creative + /cf-critic produces generation first, then critique
- /cf-creative + /cf-simplify produces many options, then strips to essentials

5-field composition tested and working without degradation.

## Usage

1. Detect appropriate field(s) from request type
2. Apply the constraints from that field
3. Combine multiple fields when useful
4. Announce active fields: `[Context Fields: /cf-debug + /cf-empathy]`
5. If no field clearly applies, respond normally

## The Four Primitives

Every field is built from these components:

1. **Inhibition**: "Do not X" - Creates a blocker that must be resolved
2. **Forcing Function**: "What/Why/How?" - Redirects processing
3. **Meta-monitor**: "Before X, do Y" - Creates a checkpoint
4. **Scope Bound**: "Under what conditions..." - Forces explicit limitation

## Creating New Fields

Use `/cf-generate` or follow this template:

```
Do not [blocker 1 - common failure mode].
Do not [blocker 2 - common failure mode].
Do not [blocker 3 - common failure mode].
[Forcing function question]?
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/good-night-oppie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
