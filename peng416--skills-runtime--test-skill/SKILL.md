---
name: test-skill
description: Use when working with a simple calculator skill for testing function calling. Can perform basic arithmetic operations like addition, subtraction, multiplication, and division.
metadata:
  author: peng416
---

# Test Skill - Calculator

## What to do
1. Parse the user's arithmetic request (e.g., "calculate 5 + 3", "what is 10 * 20")
2. Perform the calculation
3. Return the result in a clear format

## Supported Operations
- Addition (+)
- Subtraction (-)
- Multiplication (*)
- Division (/)

## Output format
- Confirm the operation: "Calculating [expression]..."
- Show the result: "Result: [number]"
- Add a friendly closing message

## Example
User: "What is 15 + 25?"
Response: 
```
Calculating 15 + 25...
Result: 40
Hope this helps! 🧮
```

## Notes
- For division by zero, return an error message
- Support both integer and floating-point numbers
- Handle invalid expressions gracefully

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/peng416) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
