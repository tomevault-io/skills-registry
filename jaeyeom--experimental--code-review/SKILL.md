---
name: code-review
description: Comprehensive code review checklist. Use when asked to review code, PRs, pull requests, or before committing changes. Use when this capability is needed.
metadata:
  author: jaeyeom
---

# Code Review Skill

## Key Focus Areas

### Code Quality
-   **DRY (Don't Repeat Yourself)**: Extract duplicated code.
-   **Error Handling**: Ensure proper error wrapping, propagation, and logging.
    -   *Go*: Do not log and bubble up (avoids duplicate logs).
-   **Resource Cleanup**: Use `defer` (Go) or RAII patterns (C++).
-   **Clear Naming**: meaningful names for functions and variables.
-   **Simplicity**: Prioritize simple solutions over complex ones.
-   **Organization**: Keep files concise (200-300 lines).

### Language-Specific Checks
-   **Go**: Goroutine safety, error handling, interface usage.
-   **Python**: PEP 8, type hints.
-   **C++**: RAII, const correctness.
-   **Protobuf**: Backward compatibility.

## Best Practices
1.  Be constructive and specific.
2.  Focus on significant issues, not nitpicks.
3.  Acknowledge good practices.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaeyeom) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
