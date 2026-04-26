---
name: godot-doc-lookup
description: Look up Godot documentation for a class, method, or topic. Use when you need API details, tutorial guidance, or code examples from the Godot docs. Use when this capability is needed.
metadata:
  author: jorge-d-robles
---

# Godot Documentation Lookup

Invoke the `godot-docs` agent for documentation lookup.

**Query:** $ARGUMENTS

Run this command:

```
Task(subagent_type="godot-docs", prompt="Look up $ARGUMENTS. Return class inheritance, key properties, methods, signals, code examples, and best practice notes.")
```

After the agent returns its summary, present the structured results to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jorge-d-robles) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
