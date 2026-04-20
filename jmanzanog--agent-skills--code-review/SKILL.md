---
name: code-review
description: Performs a comprehensive Code Review comparing the current state with the main branch. Use when this capability is needed.
metadata:
  author: jmanzanog
---

# Code Review Skill

This skill performs a complete Code Review by comparing the current changes (commits + working directory) against the base branch (`main` or `origin/main`).

## Agent Instructions

When executing this skill, rigorously follow these steps:

1.  **Get Changes**:
    Execute the helper script appropriate for the current Operating System.

    *   **Windows (PowerShell)**:
        ```powershell
        powershell -ExecutionPolicy Bypass -File "$HOME\.opencode\skills\code_review\get_changes.ps1"
        ```
    *   **Linux / macOS (Bash)**:
        ```bash
        bash "$HOME/.opencode/skills/code_review/get_changes.sh"
        ```

2.  **Analyze the Diff**:
    Read the command output. If the diff is too long, process it in chunks or attempt to summarize.

3.  **Perform Comprehensive Review**:
    Act as an **Expert Senior Backend Engineer in DDD**. Analyze the following points:
    *   **Architecture (DDD)**: Layers (Domain, Use Cases, Infra), Abstractions, DI.
    *   **Code Quality**: SOLID, naming, error handling.
    *   **Security**: Validations, secrets.
    *   **Tests**: Coverage, edge cases.
    *   **Performance**: N+1, complexity.

4.  **Generate Report**:
    Present the report in **Markdown**.
    *   **Language**: **MUST be in Spanish** (native-level technical explanations). Variable names/Code in **English**.
    *   Generate a **Mermaid** diagram if changes impact architecture.
    *   Use the format: **Resumen**, **Hallazgos Críticos**, **Sugerencias de Mejora**, **Conclusión**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jmanzanog) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
