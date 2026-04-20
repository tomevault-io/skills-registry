---
name: build
description: Build the project. Use when the user says "build", "compile", or after making code changes that need compilation. Use when this capability is needed.
metadata:
  author: opmau
---

# /build — Compile the project

Run the project build and report results.

## Steps

1. Run the build command:
   ```
   [YOUR BUILD COMMAND HERE]
   ```

2. Parse the output for errors and warnings

3. Report results in this format:
   ```
   BUILD: [PASS/FAIL]
   Errors: [count]
   Warnings: [count]
   [If failed: first error with file:line]
   ```

4. If the build fails, identify the likely cause from the error output. Do NOT attempt to fix — just report.

## Arguments

- No arguments: build all targets
- `$ARGUMENTS`: build specific target if provided

## Notes

- Do not attempt fixes automatically
- If build was triggered after code changes, note which changed files may be related to errors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/opmau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
