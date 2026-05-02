---
name: code-review
description: Use when working with a comprehensive guide and set of instructions for performing code reviews on the codebase.
metadata:
  author: hoanghd218
---

# Code Review Skill

This skill provides a structured approach to reviewing code. Use this when asked to review code or when you want to ensure the quality of changes before finalizing them.

## 1. Preparation
-   **Understand the Goal**: Read the user request and any associated task artifacts to understand what the code is *supposed* to do.
-   **Context**: Ensure you have the latest context. Run `git status` or check the file content if unsure.

## 2. Review Checklist

### Functionality
-   [ ] **Correctness**: Does the code do what it claims to do?
-   [ ] **Edge Cases**: Are edge cases handled (e.g., null values, empty lists, error states)?
-   [ ] **Bugs**: Are there any obvious bugs?

### Code Quality
-   [ ] **Readability**: Is the code easy to understand? Are variable names descriptive?
-   [ ] **Complexity**: Is the logic overly complex? Can it be simplified?
-   [ ] **Duplication**: Is there repeated code that should be refactored into a function or component?
-   [ ] **Comments**: Are complex sections commented? Are comments accurate?

### Style & consistency
-   [ ] **Conventions**: Does the code follow the project's existing coding style?
-   [ ] **Formatting**: Is the indentation and spacing consistent?

### Performance
-   [ ] **Efficiency**: Are there any obvious performance bottlenecks (e.g., nested loops, unnecessary re-renders in React)?
-   [ ] **Resources**: Are resources (files, connections) properly managed/closed?

### Security
-   [ ] **Input Validation**: Is user input validated?
-   [ ] **Secrets**: Are there any hardcoded secrets or API keys? (Flag immediately!)

## 3. Action Items
-   If you find issues, document them clearly.
-   If you can fix them easily and it's within the scope of your current task, fix them.
-   If they are outside the scope or require user decision, list them as feedback.

## 4. Tools
-   Use `grep_search` to find usages of changed functions to ensure no breaking changes.
-   Use `run_command` to run tests if available (`npm test`, etc.).
-   Use `view_file` to examine related files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoanghd218) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
