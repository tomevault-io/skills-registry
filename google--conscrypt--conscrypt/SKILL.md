---
name: conscrypt-formatting
description: >- Use when this capability is needed.
metadata:
  author: google
---

# Conscrypt Formatting

When modifying C++, C, or Java files in the Conscrypt project, you **MUST** run
the project's custom formatting script. The standard `g4 fix` or global
formatters are not sufficient as Conscrypt uses a specific configuration.

## Critical Rules

1.  **Run after modifications**: Always run the formatter *after* making any
    changes to `.java`, `.cc`, `.h`, `.cpp`, or `.c` files in Conscrypt, and
    *before* creating a CL, uploading, or submitting.
2.  **Files must be opened**: The formatting script only processes files that
    are currently **opened for edit** in your VCS (e.g., via `g4 edit`, `hg
    edit`, etc.). Ensure your modified files are opened before running the
    script.

## How to Format

Run the Python script located in the Conscrypt root directory:

```bash
python3 third_party/java_src/conscrypt/fix_format.py
```

---
> Source: [google/conscrypt](https://github.com/google/conscrypt) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
