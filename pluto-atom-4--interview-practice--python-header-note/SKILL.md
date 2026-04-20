---
name: python-header-note
description: Add comprehensive header notes to Python scripts with problem statements, algorithm explanations, and multiple summary variations (30-second pitch, rapid-fire version, and ultra-minimal one-liner). Use this skill when adding detailed documentation headers to Python interview practice files, algorithm implementations, or educational scripts. Use when this capability is needed.
metadata:
  author: pluto-atom-4
---

# Python Header Note Skill

## Overview

This skill adds comprehensive header documentation to Python scripts, ideal for interview preparation, algorithm implementations, and educational content. It creates structured header notes with multiple summary levels and detailed explanations.

## When to Use

Use this skill when you need to:
- Add detailed header notes to Python algorithm files
- Document interview practice problems and solution approach
- Document design decisions and key concepts
- Document multiple explanation formats (technical, pitch, rapid-fire)
- utilize the note as a confidence booster and communication aid during interviews

> **For usage instructions:** See [README.md](README.md) for detailed workflows on invoking this skill in Claude Code Agent and Copilot CLI.

## Header Structure

A python-header-note should include:

1. **Problem Statement** - Clear description of what the code solves
2. **Whiteboard Coding Challenge Notes** - The algorithm or technique to be used for this problem 
   - Ultra-minimal one-liner (core concept in a sentence)
   - Complexity Analysis (time and space)
3. **Algorithm Explanation** - How the solution works
   - Key concepts with "why/how" explanations
4. **Algorithm Logic** - High-level flowchart of how the algorithm executes
   - Numbered steps describing the flow
   - Key decision points and termination conditions
5. **Summary Variations**:
   - 30-second pitch (medium detail for quick understanding)
   - Rapid-fire version (key points only)
6. **Use Cases** - Where/when this solution is applicable

## Example Format

```python
"""
## Problem Statement

[1-2 sentence problem description with goal and context]

## Whiteboard Coding Challenge Notes

* For this problem, I'm using [approach/algorithm name]:

* **Ultra-Minimal One-Liner**:

- [Single sentence capturing the essence]

* **Complexity Analysis**:

- **Time Complexity:** [Expression with explanation]
- **Space Complexity:** [Expression with explanation]

## Algorithm Explanation

[Brief overview of why this approach is suitable]

* Key Concepts:

  - [Concept 1: Why/How?]
[Detailed explanation with implementation considerations]

  - [Concept 2: Why/How?]
[Detailed explanation with implementation considerations]

## Algorithm Logic

1. [Step 1: initialization and setup]
2. [Step 2: core algorithm logic]
3. [Step 3: main iteration/processing]
4. [Step 4: termination and return]

## Summary Variations

* **30-Second Pitch**:

[Concise explanation suitable for quick verbal communication]

* **Rapid-Fire Version**:

- [Key point 1]
- [Key point 2]
- [Key point 3]

## Use Cases:

[Where/when this solution is applicable]
"""

# Implementation code follows...
```

---

## Step-by-Step Implementation Guide

### 1. Problem Statement Section
**What to include:**
- Clear problem objective (1-2 sentences)
- Key constraint or goal (space, time, specific requirement)
- Context (why this matters in interviews)

**Example:**
```
Merge two sorted arrays in place without using extra space. 
The goal is to achieve this efficiently by minimizing comparisons and swaps. 
This tests understanding of in-place algorithms and optimization techniques.
```

### 2. Whiteboard Coding Challenge Notes

**What to include:**
- Algorithm/approach name (e.g., "GAP method")
- Brief rationale for choosing this approach
- Complexity analysis (time and space) with explanations

**Ultra-Minimal One-Liner:**
- Single sentence
- Captures essence for quick reference
- Includes algorithm name + complexity

**Example:**
```

For this problem, I'm using an in-place merging technique based on the GAP method, which allows us to merge two sorted arrays efficiently without extra space.

- Ultra-Minimal One-Liner:
Use the GAP method to merge in place with O((n+m) log(n+m)) time and O(1) space.

- Complexity Analysis:
  - Time Complexity: O((n+m) log(n+m)) due to the gap reduction and comparisons across both arrays.
  - Space Complexity: O(1) since we are merging in place without extra data structures.
```

### 3. Key Concepts Section

**What to include:**
- 2-3 critical design decisions
- For each: explain WHY (motivation) and HOW (implementation)
- Include code snippets if they clarify the concept

**Template for each concept:**
```
- [What is this concept?]
[Why is it important? What problem does it solve?]
[How is it implemented? Any trade-offs?]
```

**Example:**
```
- Why initialize gap as `n + m` and reduce using `(gap + 1) // 2`?
The GAP method initialization to combined length ensures far-apart elements 
are compared first, resolving large inversions early. The reduction formula 
`(gap + 1) // 2` ensures controlled gap decrease, eventually reaching 1 for 
full sort completion.
```

### 4. Logic Section
**What to include:**
- Numbered steps of the algorithm
- High-level description (not pseudo-code, not line-by-line)
- Flow and decision points

**Format:**
```
1. [Initialize variables with purpose]
2. [Define helper functions/structures with purpose]
3. [Main loop: what condition? what operations?]
4. [Termination: when and why does it stop?]
```

### 5. Summary Variations
**Create two formats:**

**30-Second Pitch:** 
- Natural speech pattern
- Suitable for verbal explanation
- Includes key algorithm name and main benefit

**Rapid-Fire Version**:
- Bullet points
- Key techniques and trade-offs
- "What would you say if interrupted?"

---

## Key Concepts to Explain

For most interview problems, address these design decisions:

| Question | Why Explain | How to Address |
|----------|------------|-----------------|
| Why this algorithm? | Shows problem understanding | Justify vs alternatives |
| Why these data structures? | Tests design knowledge | Explain space/time trade-offs |
| Why this initialization? | Shows attention to edge cases | Explain boundary conditions |
| Why these helper functions? | Shows code quality thinking | Explain abstraction benefits |
| Why this formula/formula? | Shows mathematical reasoning | Explain derivation or inspiration |

---

## Common Pitfalls to Avoid

❌ **Too brief:** "Use gap reduction" → ✅ "Initialize gap to n+m for early inversion resolution, reduce via (gap+1)//2 for controlled decrease"

❌ **Implementation details:** Focus on WHY, not line-by-line HOW → ✅ Explain the concept, not every line of code

❌ **No complexity analysis:** Always include time and space → ✅ Provide both with clear explanation

❌ **Single explanation:** Redundant for interviews → ✅ Provide 3 formats (pitch, rapid-fire, one-liner)

❌ **Missing context:** Assumes interviewer knows the problem → ✅ Always include Problem Statement section

---

## Quality Checklist

Before finalizing a header note, verify:

- [ ] **Problem Statement** is clear and interview-contextualized
- [ ] **Key Concepts** explain WHY (motivation) not just HOW (implementation)
- [ ] **Logic** section describes algorithm flow at high level
- [ ] **30-Second Pitch** is natural and conversational
- [ ] **Rapid-Fire Version** uses clear bullet points
- [ ] **Ultra-Minimal One-Liner** captures essence in one sentence
- [ ] **Complexity Analysis** includes both time and space with explanation
- [ ] **Use Cases** section contextualizes real-world applicability
- [ ] No code implementation details in header (implementation follows below)
- [ ] Consistent formatting and markdown structure


---

## Tags
`#interview-prep` `#documentation` `#header-note` `#skill` `#interview-coaching` `#algorithm-explanation` `#code-documentation`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluto-atom-4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
