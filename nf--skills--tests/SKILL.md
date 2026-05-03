---
name: tests
description: Guidelines for writing tests Use when this capability is needed.
metadata:
  author: nf
---

# Writing tests

## Variable names

When comparing expected results from actual results use the variable names
"want" and "got", or if there are multiple such variables "wantFoo", "gotFoo".

In the test failure messages show the "got" before the "want"
    t.Errorf("Sprintf returned %q, want %q", got, want)

## Golden files

When there is substantial output from the code under test, prefer to store
the expected results in a "golden" file (for structured output it might be
in JSON format).

Avoid producing such golden files by hand. Make it so that the test itself can
(re-)generate the golden file if run with the GENERATE_GOLDEN environment
variable set. Use this method to generate the golden file in the first place.

## Go-specific guidelines

### Use go-cmp

When comparing structured data or big strings/[]bytes, use the go-cmp library
(import "github.com/google/go-cmp/cmp") and its cmp.Diff function.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
