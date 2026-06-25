---
name: compile-warning-handler
description: Compiler warning triage for HPC parallelization. Use when deciding whether to proceed with job execution after compile warnings. Use when this capability is needed.
metadata:
  author: Katagiri-Hoshino-Lab
---

# Compile Warning Handler

## Decision Matrix

### Block job execution
- Parallelization disabled warnings
- Data race potential
- Memory access pattern issues
- Directive ignored warnings

### Safe to proceed
- Optimization suggestions
- Deprecated feature warnings
- Performance improvement hints

## Workflow
1. Save output: `make 2>&1 | tee compile_vX.Y.Z.log`
2. Classify warnings (block vs safe)
3. Record in ChangeLog.md `compile.status: warning`, `compile.message: "..."`
4. Proceed or fix based on classification
5. If uncertain, consult SE

---
> Source: [Katagiri-Hoshino-Lab/VibeCodeHPC](https://github.com/Katagiri-Hoshino-Lab/VibeCodeHPC) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
