---
name: technical-writing
description: Write technical content (tutorials, documentation, blog posts, explainers) in a warm, conversational, Nystrom-inspired style. Use when creating educational technical writing that should feel approachable rather than dry—programming tutorials, concept explanations, technical blog posts, documentation with personality, or any content teaching technical topics to developers. Triggers on requests like "write a tutorial about X", "explain Y in a friendly way", "create a technical blog post", or "make this documentation more engaging". Use when this capability is needed.
metadata:
  author: vuonghv
---

# Technical Writing Skill

Write technical content that teaches effectively while remaining genuinely enjoyable to read. This style is modeled after Robert Nystrom's *Crafting Interpreters*—widely regarded as one of the best-written programming books.

## Core Voice Principles

### Conversational Warmth
Address the reader directly. Use "you", "we", and "I" freely. Write as if explaining to a smart friend, not lecturing to a student.

```
❌ "The developer must initialize the buffer before use."
✅ "Before we can use the buffer, we need to initialize it."
```

### Honest About Difficulty
Acknowledge when something is hard or boring—then make it manageable. Never pretend complexity doesn't exist; help readers through it.

```
❌ "Simply implement the garbage collector."
✅ "Garbage collection is genuinely tricky. Let's take it one piece at a time."
```

### Self-Deprecating Humor
Punctuate technical content with light jokes, often at your own expense or about the absurdity of the subject. Never forced; always in service of the explanation.

```
✅ "This is a comically inefficient way to calculate Fibonacci numbers, but it stress-tests our interpreter nicely."
✅ "Compilers, like dogs, are eager to please but occasionally need explicit instructions."
```

### Vivid Analogies
Make abstract concepts tangible through unexpected comparisons. Good analogies create mental handholds.

```
✅ "You can't polish an AMC Gremlin into an SR-71 Blackbird."
✅ "The ring buffer is the quiet workhorse of systems programming."
```

## Structure Guidelines

### Opening
- Start with an epigraph (optional but effective)—a slightly tangential quote that sets the mood
- Open with genuine enthusiasm or honest framing
- Establish why the reader should care within the first few paragraphs

### Body
- Build incrementally: each section should depend only on what came before
- Include working code, not pseudocode—readers should be able to run examples
- Interleave explanation with implementation
- Use asides for historical context, etymology, or tangential insights (enriching, not required)

### Closing
- End with a "Challenges" or "Exercises" section that pushes readers beyond the guided path
- Challenges should explore, not review—they extend learning rather than test retention

## Formatting Rules

### Minimal Formatting
- Prefer prose paragraphs over bullet lists
- Use headers sparingly—only for major section breaks
- Bold and italics for emphasis, not decoration

### Code Presentation
- Show complete, runnable code
- Explain code in surrounding prose, not inline comments
- Build up complex code incrementally across snippets

### Tone Markers to Avoid
- Academic stiffness ("It should be noted that...")
- Excessive hedging ("It might perhaps be the case...")
- Hollow enthusiasm ("This amazing feature...")
- Condescension ("As any programmer knows...")

## Quick Reference: Voice Checklist

Before finalizing, verify:

1. **Direct address** — Does it use "you" and "we"?
2. **Honest framing** — Does it acknowledge difficulty without dramatizing?
3. **At least one good analogy** — Is there a concrete mental image?
4. **Light humor** — Is there at least one moment of levity?
5. **Working code** — Can examples actually run?
6. **Incremental revelation** — Does complexity build gradually?
7. **Challenges** — Does it push readers to explore further?

## Example Patterns

### Introducing a Concept
```
The core insight of a ring buffer is almost embarrassingly simple: when you 
reach the end of the array, wrap around to the beginning.

That's it. That's the whole idea.
```

### Acknowledging Complexity
```
These are design decisions, not implementation details. The ring buffer gives 
you a foundation; what you build on it depends on your constraints.
```

### Tangential Aside
```
This is why it's called a "ring" buffer. Or a "circular" buffer. Or sometimes 
a "cyclic" buffer. Data structures have commitment issues when it comes to names.
```

### Closing a Section
```
And now you know how it works. Go forth and circulate.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuonghv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
