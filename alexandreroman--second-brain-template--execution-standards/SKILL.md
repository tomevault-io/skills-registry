---
name: execution-standards
description: Enforces cross-platform portability and safe execution conventions for all scripts and shell commands. Always apply when running scripts or shell commands. Use when this capability is needed.
metadata:
  author: alexandreroman
---

# Execution Standards

## Portability

All skills and scripts MUST be portable across Windows, macOS, and Linux. Avoid OS-specific commands or paths. Use Python for scripts instead of platform-specific shell scripts.

## Python

ALWAYS run Python scripts with the `python` CLI (not `python3`).

## Shell Safety

When executing shell commands, ALWAYS quote arguments that may contain special characters (e.g., URLs with `?`, `&`, whitespace) to prevent shell parsing errors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexandreroman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
