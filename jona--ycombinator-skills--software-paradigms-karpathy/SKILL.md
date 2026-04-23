---
name: software-paradigms-karpathy
description: Explains Andrej Karpathy's framework for understanding the three paradigms of software (1.0- traditional code, 2.0- neural network weights, 3.0- LLM prompts). Use when users ask about software paradigm shifts, the evolution of programming, how LLMs fit into software development history, Software 1.0/2.0/3.0 distinctions, prompt engineering as programming, or when they need to explain or apply Karpathy's mental model for understanding modern AI development. Also useful when discussing how to think about building software in the AI era, choosing between traditional code vs neural nets vs LLM prompts, or explaining the significance of "programming in English. Use when this capability is needed.
metadata:
  author: jona
---

# Software Is Changing (Again) - Karpathy Framework

This skill provides Andrej Karpathy's framework for understanding the fundamental shift in how software is written and deployed in the AI era.

## Core Mental Model: Three Software Paradigms

### Software 1.0: Traditional Code
- **What it is**: Human-written instructions (Python, C++, JavaScript, etc.)
- **How it's created**: Developers write explicit logic
- **Where it lives**: GitHub, traditional repositories
- **Example**: Writing Python to classify sentiment with explicit rules

### Software 2.0: Neural Network Weights
- **What it is**: Parameters of trained neural networks
- **How it's created**: Curate datasets → run optimizer → produce weights
- **Where it lives**: Hugging Face (the "GitHub of Software 2.0"), Model Atlas
- **Example**: Training a neural network on labeled sentiment data
- **Key insight**: You don't write this code directly; you tune it through data

### Software 3.0: LLM Prompts
- **What it is**: Natural language instructions that program LLMs
- **How it's created**: Write prompts in English (or other natural languages)
- **Where it lives**: Embedded in applications, prompt libraries
- **Example**: Few-shot prompting an LLM with sentiment examples
- **Key insight**: Programming computers in our native language

## Paradigm Selection Guide

Determine which paradigm to use for a given task:

### Use Software 1.0 (Traditional Code) When:
- Logic is deterministic and well-defined
- Performance/latency is critical
- Behavior must be exactly reproducible
- Compliance requires auditable logic
- The problem has clear algorithmic solutions

### Use Software 2.0 (Neural Networks) When:
- Pattern recognition is required (images, audio, signals)
- Training data is abundant and high-quality
- Fixed-function transformation is needed (image → categories)
- Latency requires optimized, compiled models
- The task is well-scoped and doesn't require reasoning

### Use Software 3.0 (LLM Prompts) When:
- Flexibility and generalization matter
- Tasks require reasoning or common sense
- Rapid iteration is more important than optimization
- The problem involves natural language understanding
- You need to handle edge cases gracefully

## The LLM as Operating System Mental Model

Karpathy frames LLMs as a new kind of operating system, comparable to computing circa 1960s:

```
Traditional OS                    LLM "OS"
─────────────────────────────────────────────────────
CPU                          →    LLM inference engine
RAM                          →    Context window
Disk storage                 →    Training data / weights
Programs                     →    Prompts
System calls                 →    Tool use / function calling
Multi-tasking                →    Agentic loops
```

### Implications of This Model:
- Context window is precious (like early RAM limitations)
- Prompt engineering is systems programming
- Tool use extends capabilities (like syscalls extend programs)
- We're all learning to "program" this new OS together

## Practical Framework: Analyzing a Software Task

When approaching any software task, evaluate across all three paradigms:

### Step 1: Characterize the Task
```
Questions to ask:
- Is the input/output well-defined or open-ended?
- How much does context/reasoning matter?
- What's the latency requirement?
- How often will the logic need to change?
- What data is available for training?
```

### Step 2: Map to Paradigm Strengths

| Characteristic | Best Paradigm |
|----------------|---------------|
| Deterministic logic | 1.0 |
| Perceptual processing | 2.0 |
| Flexible reasoning | 3.0 |
| Sub-millisecond latency | 1.0 or optimized 2.0 |
| Rapid prototyping | 3.0 |
| Large-scale pattern matching | 2.0 |

### Step 3: Consider Hybrid Approaches

Modern systems often combine paradigms:

```
Example: Tesla Autopilot Stack (as described by Karpathy)

┌─────────────────────────────────────────┐
│           Software 1.0 (C++)            │  ← Vehicle control, safety systems
├─────────────────────────────────────────┤
│        Software 2.0 (Neural Nets)       │  ← Perception, prediction
├─────────────────────────────────────────┤
│     Inputs: Cameras, Sensors, Maps      │
└─────────────────────────────────────────┘
```

```
Example: Modern AI Application

┌─────────────────────────────────────────┐
│      Software 3.0 (LLM Prompts)         │  ← Reasoning, user interaction
├─────────────────────────────────────────┤
│   Software 2.0 (Embeddings, Classifiers)│  ← Retrieval, routing
├─────────────────────────────────────────┤
│      Software 1.0 (Python/APIs)         │  ← Orchestration, I/O, tools
└─────────────────────────────────────────┘
```

## Explaining the Paradigm Shift

When communicating these concepts to others:

### For Technical Audiences:
- Emphasize that neural network weights ARE code (just compiled differently)
- Draw parallels to compilation: data → optimizer → weights ≈ source → compiler → binary
- Highlight that prompt engineering is a legitimate programming discipline

### For Non-Technical Audiences:
- Focus on "programming in English" as the key innovation
- Use the OS analogy: LLMs are like a new kind of computer we're learning to use
- Emphasize that software hasn't changed this fundamentally in 70 years

### Key Talking Points:
1. "Software 1.0 is code you write. Software 2.0 is code you train. Software 3.0 is code you describe."
2. "Hugging Face is to neural networks what GitHub is to traditional code."
3. "We're at the 1960s of LLM computing—everything is being figured out in real-time."

## Historical Context

### Why This Matters Now:
- Neural networks were previously seen as "just another classifier" (like decision trees)
- The key shift: neural networks became **programmable** with LLMs
- Fixed-function (image → category) evolved to general-purpose (prompt → response)

### The Significance of English as Programming Language:
- First time computers are programmed in natural human language
- Dramatically lowers barrier to "programming"
- Changes who can build software and how

## Common Questions and Responses

**Q: Is prompt engineering "real" programming?**
A: Yes. Prompts are programs that control a new type of computer (the LLM). The programming language happens to be English, but it still requires systematic thinking about inputs, outputs, and behavior.

**Q: Will Software 3.0 replace 1.0 and 2.0?**
A: No. Each paradigm has strengths. The trend is toward hybrid systems that combine all three appropriately.

**Q: Where should I focus my learning?**
A: Understand all three paradigms. The most valuable skill is knowing when to apply each and how to combine them effectively.

**Q: How do I think about the "code" I see now that mixes Python and English prompts?**
A: This is the new normal. Modern codebases increasingly contain Software 1.0 (orchestration logic), Software 2.0 (model weights/calls), and Software 3.0 (prompts) interleaved together.

## Quick Reference: Paradigm Comparison

```
┌────────────┬─────────────────┬─────────────────┬─────────────────┐
│            │  Software 1.0   │  Software 2.0   │  Software 3.0   │
├────────────┼─────────────────┼─────────────────┼─────────────────┤
│ Medium     │ Code (Python,   │ Weights         │ Prompts         │
│            │ C++, etc.)      │ (parameters)    │ (English)       │
├────────────┼─────────────────┼─────────────────┼─────────────────┤
│ Created by │ Writing logic   │ Training on     │ Describing      │
│            │                 │ data            │ behavior        │
├────────────┼─────────────────┼─────────────────┼─────────────────┤
│ Repository │ GitHub          │ Hugging Face    │ (emerging)      │
├────────────┼─────────────────┼─────────────────┼─────────────────┤
│ Debugging  │ Step through    │ Inspect         │ Iterate on      │
│            │ code            │ activations     │ prompts         │
├────────────┼─────────────────┼─────────────────┼─────────────────┤
│ Iteration  │ Edit code       │ Retrain model   │ Edit prompt     │
│            │                 │ (LoRA, etc.)    │                 │
└────────────┴─────────────────┴─────────────────┴─────────────────┘
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jona) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
