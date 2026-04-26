---
name: tdd-refactor-expert
description: Guidance and checklists for the Refactor phase of TDD, ensuring clean code without breaking tests. Use when this capability is needed.
metadata:
  author: jcorpac
---

# TDD Refactor Expert

Refactoring is about improving the design of existing code without changing its external behavior.

## Refactoring Checklist
- [ ] **Descriptive Naming**: Are variables, functions, and classes named after what they *do*?
- [ ] **Small Units**: Can large functions be broken down? (Goal: < 20 lines).
- [ ] **Dry Principle**: Is there any duplicated logic that can be extracted?
- [ ] **Single Responsibility**: Does each class/function do only one thing?
- [ ] **Consistent Style**: Does the code follow the project's style guide?

## Common Patterns
1. **Extract Method**: Move a fragment of code to a new function.
2. **Rename Variable**: Clarify the purpose of a variable.
3. **Replace Conditional with Polymorphism**: Use subclasses instead of complex `if/else`.
4. **Introduce Parameter Object**: Group related parameters into a class.

## Safety First
1. Run all tests (Green).
2. Apply **one** small refactoring.
3. Run all tests again (Should still be Green).
4. Repeat.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
