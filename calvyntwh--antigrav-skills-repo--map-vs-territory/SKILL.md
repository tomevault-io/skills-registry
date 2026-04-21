---
name: map-vs-territory
description: Empiricism and Reality Checking. Prioritizing runtime truth over documentation. Use when debugging discrepancies, validating APIs, or when code comments might be outdated. Use when this capability is needed.
metadata:
  author: calvyntwh
---

# Map vs. Territory (The Reality Check)

> "The map is not the territory." - Alfred Korzybski

## When to Use
*   **Debugging:** When "it should work" but doesn't.
*   **Integration:** When using 3rd party APIs or libraries.
*   **Legacy Code:** When comments don't match the code.

## The Protocol: The Reality Check
Do not trust the Map (Docs, Comments, Variable Names).
Trust the Territory (Logs, Memory, Disk, Network).

### 1. The Doubt
Identify the "Map" you are relying on.
*   *"The comment says this function returns a User."*
*   *"The variable is named `isValid`."*
*   *"The API docs say it returns 200 OK."*

### 2. The Probe
Active verify the Territory.

> [!IMPORTANT]
> **DO NOT READ CODE. RUN CODE.**
> Reading code is reading the Map. Running code is touching the Territory.

*   *Action:* `print(type(user))` -> Is it actually a User object? or a Dict? or None?
*   *Action:* `console.log(isValid)` -> Is it `true`? `1`? `"true"`?
*   *Action:* Inspect the raw HTTP body.

> [!TIP]
> **Fallback:** If you cannot run code directly, ask the user to run it and report the output.

### 3. The Update
If Map $\neq$ Territory, **The Map is Wrong.**
*   Update the docs/comments.
*   Rename the variable (`isValid` -> `shouldBeValid`).
*   Fix the mental model.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/calvyntwh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
