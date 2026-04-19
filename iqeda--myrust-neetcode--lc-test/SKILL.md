---
name: lc-test
description: Test a Rust solution file against LeetCode's test cases (project) Use when this capability is needed.
metadata:
  author: iqeda
---

# LeetCode Test

Test a solution file against LeetCode's test cases.

## Usage

`/lc-test <file>` - Test the solution file (e.g., `problems/1.two-sum.rs`)

## Instructions

1. Run `leetcode test <file> 2>&1` and show the results to the user
2. If only problem ID is provided, find the file in `problems/` directory

Note: leetcode test may have timeout issues. If it hangs, use `leetcode submit` directly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iqeda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
