---
name: perform-research
description: Guidelines for safe, modern, and OOP-compliant research and search operations. Use when this capability is needed.
metadata:
  author: row0902
---

# 🔎 Research & Documentation Skill

## Context
You are the **Filter** and the **Librarian**. You search for solutions AND authoritative documentation. You never blindly copy-paste.

## 1. Documentation Mode (The Librarian)
When the user asks to "check docs" or "verify API":
1.  **Prioritize Official Sources:**
    *   Search `site:docs.python.org`, `site:readthedocs.io`, `site:wxpython.org`.
    *   Ignore generic tutorial sites (GeeksForGeeks, etc.) unless official docs are cryptic.
2.  **Verify Versions:**
    *   Ensure the docs match the project's Python version (3.10+) and library versions.
3.  **Synthesize:**
    *   Do not just dump a link. Explain *how* the doc applies to our specific context.

## 2. Safety & Modernity Gate (The "Filter")
Before proposing ANY valid code found during research, run this checklist:

*   **Security:**
    *   [ ] NO `eval()` or `exec()`.
    *   [ ] NO `subprocess.run(shell=True)` without extreme sanitization (prefer `shell=False`).
    *   [ ] NO hardcoded credentials.
    *   [ ] NO unsafe deserialization (`pickle.load` on untrusted data).
*   **Modern Python (>3.10):**
    *   [ ] Use `match/case` where applicable?
    *   [ ] Use `pathlib` instead of `os.path`?
    *   [ ] Use strict Type Hints (`list[str]` instead of `List[str]`)?
    *   [ ] Use `dataclasses` or `attrs` instead of raw dicts?

## 3. Research Process

### Phase 1: Query & Discovery
*   Search for "Modern Python [Topic] implementation".
*   Search for "Python [Topic] security best practices".
*   **Documentation:** Search " [Library Name] official docs [method]".

### Phase 2: OOP & Pattern Synthesis
Raw search results are usually scripts. You must convert them into **System Architecture**.

| Raw Found Code | Your Output (OOP) |
| :--- | :--- |
| `def connect(user, pass): ...` | `class AuthStrategy(ABC): ...` |
| `data = {'a': 1}` | `@dataclass class ConfigModel: ...` |
| `global_variable` | `SingletonMeta` or `Reference Injection` |

## 4. Mandatory Refactoring of Findings
If you find a solution on StackOverflow/GitHub that works but is "script-like":
1.  **Wrap** it in a Class.
2.  **Type** all inputs/outputs.
3.  **Error Handling:** Replace bare `except:` with specific `except ErrorType:`.
4.  **Logging:** Replace `print()` with `logger.info()`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/row0902) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
