---
name: codebase-course-creator
description: Creates structured educational courses from codebases for teaching code to students or peers. Use when onboarding new team members, creating training materials, or preparing technical presentations.
metadata:
  author: jjenksy
---

# Codebase Course Creator

Create a comprehensive, structured course that guides learners through understanding this codebase.

## Course topic/focus

$ARGUMENTS

## Phase 1: Codebase Analysis

First, thoroughly explore the codebase to understand:

1. **Architecture overview**: What are the main components and how do they connect?
2. **Entry points**: Where does execution begin? What are the key flows?
3. **Core concepts**: What domain concepts and patterns are essential to understand?
4. **Dependencies**: What external libraries/frameworks are used and why?
5. **Complexity mapping**: Which parts are simple vs. complex?

## Phase 2: Course Design

Design the course structure using pedagogical best practices:

### Learning Objectives
Define clear, measurable outcomes for each module:
- "By the end of this module, learners will be able to..."
- Use Bloom's taxonomy verbs (identify, explain, implement, analyze, evaluate)

### Prerequisite Knowledge
List what learners should already know before starting

### Learning Path
Organize content from simple → complex, concrete → abstract:
1. Start with high-level overview (the "why")
2. Progress to core concepts (the "what")
3. Deep dive into implementation (the "how")
4. Advanced topics and edge cases

## Phase 3: Course Content Generation

For each module, create:

### Module Structure
```
Module N: [Title]
├── Overview (2-3 sentences)
├── Learning objectives (3-5 bullet points)
├── Concepts
│   ├── Explanation with analogy
│   ├── ASCII diagram or visual
│   └── Code walkthrough with annotations
├── Key files to study
│   └── path/to/file.ts:42 - Why this file matters
├── Hands-on exercise
│   ├── Starter task (guided)
│   ├── Challenge task (independent)
│   └── Solution with explanation
├── Common mistakes & misconceptions
├── Knowledge check (3-5 questions)
└── Summary & next steps
```

### Content Guidelines

**Explanations**:
- Use analogies from everyday life
- Build on previous modules
- Explain the "why" before the "how"
- Include ASCII diagrams for architecture/flow

**Code Walkthroughs**:
- Use real code from the codebase with line references
- Add inline annotations explaining key lines
- Show the execution flow step-by-step
- Highlight patterns and conventions used

**Exercises**:
- Graduated difficulty (warm-up → challenge)
- Practical tasks that reinforce concepts
- Include expected outcomes and verification steps

**Knowledge Checks**:
- Mix of conceptual and practical questions
- Include "what would happen if..." scenarios
- Provide answers with explanations

## Phase 4: Output Format

Generate the course as a structured markdown document:

```markdown
# [Course Title]

## Course Overview
- **Duration**: Estimated time to complete
- **Audience**: Who this course is for
- **Prerequisites**: Required knowledge
- **Objectives**: What learners will achieve

## Course Outline
[Numbered list of all modules with brief descriptions]

---

## Module 1: [Title]
[Full module content following the structure above]

---

## Module 2: [Title]
[Continue for all modules...]

---

## Appendix
- Glossary of terms
- Additional resources
- Quick reference cheat sheet
```

## Teaching Tips

Include a "Instructor Notes" section with:
- Suggested pacing for each module
- Discussion prompts for group sessions
- Common questions learners ask
- Live coding demo suggestions
- Points to emphasize or spend extra time on

## Quality Checklist

Before finalizing, verify:
- [ ] Logical progression from simple to complex
- [ ] All key concepts are covered
- [ ] Each module has clear learning objectives
- [ ] Code references use actual file paths and line numbers
- [ ] Diagrams are clear and accurate
- [ ] Exercises are achievable and relevant
- [ ] No assumed knowledge that wasn't covered
- [ ] Summary ties back to learning objectives

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jjenksy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
