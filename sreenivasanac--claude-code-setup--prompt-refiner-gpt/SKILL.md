---
name: prompt-refiner-gpt
description: Refine prompts for GPT models (GPT-5, GPT-5.1, Codex) using OpenAI's best practices. Use when preparing complex tasks for GPT. Use when this capability is needed.
metadata:
  author: sreenivasanac
---

# GPT Prompt Refiner

## When to Use
Invoke this skill when you have a task for GPT that:
- Requires a specific persona or expertise
- Involves procedural steps
- Needs structured output
- Benefits from explicit examples

## Refinement Process

### 1. Analyze the Draft Prompt
Review for:
- [ ] Clear role/persona definition
- [ ] Step-by-step breakdown (if procedural)
- [ ] Output format specification
- [ ] Concrete examples

### 2. Apply GPT-Specific Patterns

**Role framing:**
Start with "You are a [specific role] working on [specific context]..."

**Numbered procedures:**
Break complex tasks into numbered steps that build on each other.

**Output specification:**
Be explicit: "Return as JSON", "Format as markdown with headers", etc.

**Chain of thought:**
For reasoning tasks, add: "Think through this step by step."

### 3. Structure the Prompt

**Effective order for GPT:**
1. Role definition (who/what)
2. Context (background info)
3. Task (what to do)
4. Steps (how to do it, if procedural)
5. Output format (what to return)
6. Examples (optional clarification)

### 4. Output the Refined Prompt
Present with:
- Clear role statement
- Numbered steps where applicable
- Explicit output requirements

## Example Transformation

**Before:**
"Review this code for security issues"

**After:**
You are a senior security engineer conducting a security audit of a Node.js payment processing service.
Context: This service handles credit card transactions and communicates with Stripe’s API. It runs in AWS ECS.
Task: Review the code in src/payments/ for security vulnerabilities.
Steps:
Check for proper input validation on all endpoints
Verify secrets are not hardcoded or logged
Review authentication and authorization logic
Check for SQL injection and XSS vulnerabilities
Verify proper error handling that doesn’t leak sensitive info
Output format: Return a security report in markdown with:
Critical: Issues that must be fixed before deployment
High: Significant risks that should be addressed soon
Medium: Improvements to consider
Recommendations: General security enhancements
For each issue, include:
File and line number
Description of the vulnerability
Recommended fix with code example

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sreenivasanac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
