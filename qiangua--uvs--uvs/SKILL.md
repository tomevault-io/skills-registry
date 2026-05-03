---
name: uvs
description: Inject PEP 723 inline script dependencies into Python scripts so they can be run with `uv run`. Use when the user wants to add/update uv script headers, convert a plain Python script for uv, or run a script with `uv run`. Use when this capability is needed.
metadata:
  author: qiangua
---

Run `uvs` on the specified Python script(s) to inject or update PEP 723 inline dependency metadata.

If $ARGUMENTS is empty, look for `.py` files in the current directory that are likely standalone scripts (not part of a package) and ask the user which ones to process.

Steps:
1. Check that `uvs.py` is available — either in the current directory, on PATH, or installed. If not found, tell the user to install it first (see README).
2. Run: `uv run uvs.py $ARGUMENTS` (or `uvs $ARGUMENTS` if installed on PATH)
3. Show the output to the user.
4. If the script was updated, remind the user they can now run it with `uv run <script.py>`.

If the user passes `--dry-run`, add that flag so no files are modified.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qiangua) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
