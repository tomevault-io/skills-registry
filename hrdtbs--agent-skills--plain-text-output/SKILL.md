---
name: plain-text-output
description: Constrains agent output to plain text only: no emojis, no bold (**/__), no italics (*/_). Prefers noun-ending style (体言止め) in Japanese. Make sure to use this skill whenever the user asks for output in plain text, when generating any documentation, commit messages, PR descriptions, or when responding in an environment that requires unadorned text. Use when this capability is needed.
metadata:
  author: hrdtbs
---

# Plain Text Output

This skill ensures that all generated text is strictly plain text, free of any formatting decorations. This is crucial for maintaining compatibility across various systems and ensuring maximum readability in restricted environments like terminals.

## Formatting Rules

* **No Decoration**: Do not use Unicode emojis, bold, or italics (except inside code blocks or when quoting code). Avoiding decorations ensures the text remains completely clean and compatible with systems that do not support rich text.
* **Japanese Style**: Always use noun-ending (体言止め) by default when writing in Japanese. This creates a concise, objective, and professional tone suitable for documentation and logs.

## Examples

**Example 1: Documentation**
Input: We **must** ensure that the *new* API endpoints are fully tested before deploying! 🚀
Output: Ensure that the new API endpoints are fully tested before deploying.

**Example 2: Japanese Output**
Input: 新しい機能を追加しました。UIがとても綺麗になりましたね✨
Output: 新機能の追加。UIの改善。

**Example 3: Commit Message**
Input: ✨ **feat**: added the new login page!!
Output: feat: add new login page

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hrdtbs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
