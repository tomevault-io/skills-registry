---
name: math
description: Compute a value from a Python math expression string. Use when this capability is needed.
metadata:
  author: chaddm
---

## What I do

Run the following command to evaluate a Python math expression:

```
~/.config/opencode/skills/math/compute.py "<expression>"
```

Replace `<expression>` with any valid Python math expression, such as:
- Simple arithmetic: "2+2", "10*5", "100/4"
- More complex: "2**8", "sqrt(16)", "sin(3.14159/2)"
- With parentheses: "(5+3)*2", "((10-2)/4)+1"

## When to use me

Use me when you need to:
- Evaluate mathematical expressions programmatically
- Perform calculations with arithmetic operators (+, -, *, /, //, %, **)
- Use mathematical functions (sin, cos, tan, sqrt, log, etc.)
- Get precise numeric results from complex expressions

## Examples

```bash
# Simple arithmetic
./compute.py "2+2"
# Output: 4

# Exponentiation
./compute.py "2**10"
# Output: 1024

# Complex expression
./compute.py "(25 + 15) * 2 / 4"
# Output: 20.0
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chaddm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
