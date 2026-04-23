---
name: agi-framework-chollet
description: Provides François Chollet's framework for understanding intelligence, AGI development paths, and the limitations of current AI approaches. Use this skill when users ask about- (1) What intelligence really means and how to define AGI, (2) Why scaling pre-training alone won't achieve AGI, (3) The difference between memorized skills and fluid intelligence, (4) Test-time adaptation and its role in AGI, (5) The ARC benchmark and what it measures, (6) Type 1 vs Type 2 abstraction in AI systems, (7) Program synthesis approaches to intelligence, (8) Evaluating claims about AGI progress, or (9) Understanding the conceptual foundations needed for building generally intelligent systems.
metadata:
  author: jona
---

# François Chollet's Framework for AGI

This skill encapsulates François Chollet's theoretical framework for understanding intelligence and the path to AGI, based on his analysis of AI progress and the ARC benchmark.

## Core Concepts

### Two Definitions of Intelligence

**Minsky-style view (task-based):**
- AI performs tasks normally done by humans
- Corporate AGI definition: "model that could perform most economically valuable tasks"
- Focus on capability benchmarks

**McCarthy-style view (adaptation-based):**
- Intelligence is the ability to understand something new on the fly
- Focus on fluid reasoning, not memorized skills
- Measures generalization to novel situations

**Key distinction:** Memorized skills (static, task-specific) vs. fluid general intelligence (dynamic, adaptable).

### The Pre-Training Scaling Era (2020-2024)

**What worked:**
- Self-supervised text modeling at scale
- Predictable benchmark improvements with more compute/data
- Crushing performance on traditional benchmarks

**Why it plateaued for AGI:**
- Benchmarks measured memorized skills, not fluid intelligence
- 50,000x scale-up (2019→2024) yielded only 0%→10% on ARC
- Humans score >95% on ARC with no training
- Confusion between benchmark performance and true understanding

### Test-Time Adaptation Era (2024+)

**Core principle:** Models modify their own behavior dynamically based on specific data encountered during inference.

**Key techniques:**
- Test-time training
- Program synthesis
- Chain-of-thought synthesis
- Self-reprogramming for tasks at hand

**Evidence of progress:**
- OpenAI's O3 (fine-tuned on ARC) achieved human-level ARC performance
- All high-performing ARC approaches use test-time adaptation

### Type 1 vs Type 2 Abstraction

**Type 1 (Perceptual):**
- Pattern recognition from raw sensory data
- Continuous, gradient-based learning
- What deep learning excels at

**Type 2 (Discrete/Programmatic):**
- Symbolic, compositional reasoning
- Discrete program-like structures
- Requires on-the-fly recombination of concepts

**AGI requirement:** Both types working together—deep learning for perception, program search for reasoning.

## Analytical Workflows

### Evaluate AGI Claims

When assessing claims about AGI progress:

1. Identify the benchmark being used
2. Determine if it measures memorized skills or fluid intelligence
3. Check if the model was trained on similar data
4. Ask: "Would performance hold on genuinely novel problems?"
5. Look for test-time adaptation vs. pure pre-training

**Red flags:**
- Claims based solely on traditional benchmarks
- No evaluation on out-of-distribution tasks
- Conflating task performance with general intelligence

### Analyze AI System Architecture

When examining an AI system's potential for general intelligence:

1. Assess pre-training approach
   - What knowledge is baked in?
   - How task-specific is the training?

2. Evaluate test-time capabilities
   - Can the system adapt during inference?
   - Does it use chain-of-thought or program synthesis?
   - Can it modify its behavior for novel problems?

3. Check for both abstraction types
   - Type 1: Perceptual pattern matching
   - Type 2: Discrete symbolic reasoning

4. Determine generalization potential
   - How does it perform on ARC-style tasks?
   - Can it handle problems outside training distribution?

### Assess Intelligence Metrics

When evaluating whether a metric truly measures intelligence:

1. Apply the Chollet criteria:
   - Does it require understanding novel situations?
   - Can it be solved by memorization?
   - Is the solution space too narrow?

2. Check for the "ARC test":
   - Would 50,000x more compute dramatically improve scores?
   - If no, the metric likely measures fluid intelligence
   - If yes, it may just measure knowledge retrieval

3. Consider human baseline:
   - Humans with no training should outperform on true intelligence tests
   - Large gap between human and AI suggests memorization-based approach

## Key Frameworks

### The Compute Cost Trajectory

Historical pattern since 1940:
- Compute cost falls ~100x per decade
- No sign of stopping
- Implication: Compute alone doesn't solve intelligence

### Why Pre-Training Scaling Failed for AGI

```
Scaling pre-training → Better benchmark scores
Better benchmark scores ≠ General intelligence
General intelligence requires → Novel problem adaptation
Novel problem adaptation requires → Test-time learning
```

### The AGI Architecture Hypothesis

```
AGI = Deep Learning (Type 1) + Program Search (Type 2)
     + Test-Time Adaptation
     
Where:
- Deep Learning handles perception and pattern matching
- Program Search handles discrete reasoning and composition
- Test-Time Adaptation enables on-the-fly learning
```

## Common Questions

### "Is AGI already here with O3?"

Evaluate by asking:
- Was the model fine-tuned specifically on ARC-like data?
- Does performance generalize to other novel reasoning tasks?
- Is it truly adapting or just better memorization?

O3 shows promising signs but fine-tuning on ARC means true generalization is uncertain.

### "What's missing from current LLMs?"

Key gaps:
- True test-time learning (not just prompting)
- Program synthesis capabilities
- Type 2 discrete abstraction
- Generalization without task-specific fine-tuning

### "How should we measure AGI progress?"

Use metrics that:
- Cannot be solved by memorization
- Require novel problem understanding
- Show ceiling effects with scale alone
- Demonstrate human-like fluid reasoning

ARC exemplifies these properties.

## Terminology Reference

| Term | Definition |
|------|------------|
| Fluid intelligence | Ability to understand novel situations without prior training |
| Memorized skills | Static, task-specific capabilities from training |
| Test-time adaptation | Model modifying behavior during inference |
| ARC | Abstraction and Reasoning Corpus benchmark |
| Type 1 abstraction | Perceptual, continuous pattern recognition |
| Type 2 abstraction | Discrete, programmatic reasoning |
| Program synthesis | Generating programs to solve problems |
| Scaling laws | Predictable performance gains from more compute/data |

## Application Guidelines

### When Discussing AGI Timelines

1. Distinguish capability claims from intelligence claims
2. Ask what benchmarks support the claim
3. Check for test-time adaptation in the architecture
4. Consider whether the system generalizes beyond training

### When Designing AI Systems for Generalization

1. Include test-time adaptation mechanisms
2. Don't rely solely on pre-training scale
3. Incorporate program synthesis where possible
4. Test on out-of-distribution problems like ARC
5. Balance Type 1 and Type 2 capabilities

### When Evaluating AI Research Directions

Prioritize approaches that:
- Enable learning at inference time
- Combine neural and symbolic methods
- Demonstrate novel problem-solving
- Go beyond benchmark optimization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jona) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
