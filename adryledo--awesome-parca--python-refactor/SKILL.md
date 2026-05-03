---
name: python-refactor
description: Guidance for refactoring Python code to improve clarity and maintainability. Use this skill when the user asks to simplify Python logic, restructure functions, or improve code quality. Use when this capability is needed.
metadata:
  author: adryledo
---

# Python Refactor Skill

This skill provides practical steps for improving Python code structure and readability.

## Goals

- Improve maintainability  
- Simplify complex logic  
- Clarify naming and responsibilities  
- Remove unused or redundant code  

## Recommended Process

### 1. Analyze the structure
Identify:
- Functions, classes, modules  
- Deep nesting or long functions  
- Side effects and repeated logic  

### 2. Detect common issues
Look for:
- Unclear naming  
- Duplicate logic  
- Unreachable code  
- Long parameter lists  
- Mutable default arguments  

### 3. Apply refactoring techniques
Use patterns such as:
- Extract Function  
- Inline Variable  
- Replace Temp with Query  
- Introduce Parameter Object  
- Remove Dead Code  
- Simplify Conditional Expressions  

### 4. Produce output
Return:
- The refactored code  
- A short explanation of changes  
- Any recommended follow‑up improvements  

## Example

**Input**

[`scripts/process.py`](scripts/process.py)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adryledo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
