---
name: meta-agentic-chunking
description: Breaking complex tasks into discrete sub-steps with isolated context for maximum precision. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Meta-Agentic Chunking

Complex tasks often fail because the model tries to handle too much complexity at once.

## The Chunking Workflow
1.  **Decomposition**: Breaking the user objective into small, independent sub-tasks.
2.  **Context Isolation**: For each sub-task, only load the files and skills relevant to *that specific step*.
3.  **Handoff**: Passing only the *results* of the sub-task to the next step, rather than the entire execution history.

## Benefits
- **Reduced Hallucinations**: Smaller context means fewer distractions.
- **Higher Fidelity**: The model can follow specific instructions for a single phase (e.g., "Refactoring") without being distracted by requirements from another phase (e.g., "Deployment").

## Best Practices
- **Clear Boundaries**: Use `task_boundary` tools to signify the switch between chunks.
- **State Persistence**: Keep a "Source of Truth" document (e.g., a `plan.md`) that tracks the results of all chunks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
