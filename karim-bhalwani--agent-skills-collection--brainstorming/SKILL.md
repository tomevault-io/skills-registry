---
name: brainstorming
description: Explore user intent, requirements, and design before implementation. Use when turning ideas into designs, validating requirements through dialogue, exploring multiple approaches, or creating design documentation for features, components, and functionality changes. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# Brainstorming Ideas Into Designs

## Overview

Help turn ideas into fully formed designs and specs through natural collaborative dialogue.

Start by understanding the current project context, then ask questions one at a time to refine the idea. Once you understand what you're building, present the design in small sections (200-300 words), checking after each section whether it looks right so far.

## The Process

**Understanding the idea:**

- Check out the current project state first (files, docs, recent commits)
- Ask questions one at a time to refine the idea
- Prefer multiple choice questions when possible, but open-ended is fine too
- Only one question per message - if a topic needs more exploration, break it into multiple questions
- Focus on understanding: purpose, constraints, success criteria

**Exploring approaches:**

- Propose 2-3 different approaches with trade-offs
- Present options conversationally with your recommendation and reasoning
- Lead with your recommended option and explain why

**Presenting the design:**

- Once you believe you understand what you're building, present the design
- Break it into sections of 200-300 words
- Ask after each section whether it looks right so far
- Cover: architecture, components, data flow, error handling, testing
- Be ready to go back and clarify if something doesn't make sense

## After the Design

**Documentation:**

- Write the validated design to `docs/plans/YYYY-MM-DD-<topic>-design.md`
- Use elements-of-style:writing-clearly-and-concisely skill if available
- Commit the design document to git

**Implementation (if continuing):**

- Ask: "Ready to set up for implementation?"
- Use superpowers:using-git-worktrees to create isolated workspace
- Use superpowers:writing-plans to create detailed implementation plan

## Key Principles

- **One question at a time** - Don't overwhelm with multiple questions
- **Multiple choice preferred** - Easier to answer than open-ended when possible
- **YAGNI ruthlessly** - Remove unnecessary features from all designs
- **Explore alternatives** - Always propose 2-3 approaches before settling
- **Incremental validation** - Present design in sections, validate each
- **Be flexible** - Go back and clarify when something doesn't make sense

## Outputs & Deliverables

- **Primary Output**: Design document (markdown) with architecture, components, data flow, and error handling
- **Secondary Output**: Validated design sections with stakeholder approval
- **Success Criteria**: Stakeholders agree design is buildable and complete
- **Quality Gate**: Design ready for handoff to `implementer`

## Constraints

- **NO implementation code.** Design and architecture only.
- **NO deployment planning.**
- Must validate each section incrementally with stakeholder feedback.

## Common Pitfalls

- **Leading with Solutions**: Jumping to "build a dashboard" before understanding the actual problem. Always start with "what problem are we solving?"
- **Vague Acceptance Criteria**: Ending with "sounds good" instead of specific, measurable success criteria. Define success metrics before moving forward.
- **Skipping Tradeoff Analysis**: Not exploring alternatives leaves the team with one perspective. Always present 2-3 approaches with trade-offs.
- **Rushing Validation**: Presenting the entire design at once instead of section-by-section validation. This leads to rework and frustration.
- **Ignoring Constraints**: Designing without understanding budget, timeline, or technical limits. Ask constraints early.
- **Assuming Shared Understanding**: "We all agree what this means" leads to misalignment. Define terms explicitly and check agreement.

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Exploration | User vision | Design validation | Ask refining questions to understand intent |
| Design | Validated requirements | `architect` | Pass to architect for technical specification |
| Handoff | Approved design | `implementer` | Provide design document and acceptance criteria |
| Planning | Design scope | `concise-planning` | Feed into implementation planning |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
