---
name: rubber-ducking
description: Logic validation by verbalization. Translate code to plain English to catch semantic errors. Use before running complex logic or when debugging "it should work" failures. Use when this capability is needed.
metadata:
  author: calvyntwh
---

# Rubber Ducking (The Logic Validator)

> "By the time you've explained the problem to the duck, you've often solved it yourself." - The Pragmatic Programmer (1999)

## When to Use
*   **Before Running:** When you have written complex logic (loops, conditionals, state machines).
*   **Debugging:** When "it should work" but produces wrong output.
*   **Code Review:** When reviewing your own generated code before presenting it.

## The Protocol: Explain It to the Duck
**Constraint:** You must be able to explain the code in plain English. If you cannot, you do not understand it.

### 1. Identify the Block
Select the complex code segment. Focus on:
*   `if/else` chains
*   `for/while` loops
*   Business logic calculations
*   State transitions

### 2. Translate to English
Write a plain English sentence for each logical step.

**Example:**
```python
for i in range(len(arr) - 1):
    if arr[i] > arr[i + 1]:
        arr[i], arr[i + 1] = arr[i + 1], arr[i]
```

**Translation:**
1.  "We iterate through the array, stopping one element before the end."
2.  "If the current element is greater than the next element..."
3.  "...we swap them."

### 3. Goal Alignment Check (CRITICAL)
Compare your English description to the User's stated goal.

| User Goal | Your Translation | Match? | Action |
| :--- | :--- | :--- | :--- |
| "Sort the array" | "We swap adjacent elements if out of order" | ✅ Partial | This is Bubble Sort. Correct, but inefficient. Note limitation. |
| "Find the maximum" | "We swap adjacent elements..." | ❌ No | **STOP.** The code does not achieve the goal. Rewrite. |

### 4. The Verdict
*   **If Match:** Proceed to run/save the code.
*   **If Mismatch:** Do NOT run. Fix the logic first. The translation revealed the bug.

## Example: The Off-By-One Bug

**User Goal:** "Reverse the array in place."

**Code:**
```python
def reverse(arr):
    for i in range(len(arr) // 2):
        arr[i], arr[len(arr) - i] = arr[len(arr) - i], arr[i]
    return arr
```

**Translation (Ducking):**
1.  "Iterate through the first half of the array."
2.  "Swap element at `i` with element at `len(arr) - i`."

**Goal Check:**
*   *Problem:* `arr[len(arr) - i]` when `i=0` is `arr[len(arr)]` → **IndexError!**
*   *Fix:* Should be `arr[len(arr) - 1 - i]`.

**Result:** Bug caught *before* running. No runtime error wasted.

## Resources
*   [Detailed Research Notes](references/research.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/calvyntwh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
