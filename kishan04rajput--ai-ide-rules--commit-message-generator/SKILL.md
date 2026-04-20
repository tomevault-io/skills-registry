---
name: commit-message-generator
description: Use when generating commit messages for code changes.
metadata:
  author: kishan04rajput
---

# Commit Message Generator

When the user asks to generate a commit message or when you are preparing a commit, you **MUST** follow the format below exactly.

## Output Structure

Return the commit message as shown:

## <Short Summary of the Commit starting with ##>

<Absolute Path to File 1>
- <Specific change in File 1>
- <Another specific change in File 1>

<Absolute Path to File 2>
- <Specific change in File 2>

## Guidelines

1. **Header**: The first line must be the commit summary, starting with `##`.
2. **File Paths**: Use the file path for each file that was modified.
3. **Changes**: Under each file path, list the changes made to that file using a bullet point `-`.
4. **Grouping**: Group all changes by their respective files.

## Examples

Please refer to the `examples/` directory for concrete usage examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kishan04rajput) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
