---
name: ask-code-reviewer
description: > Use when this capability is needed.
metadata:
  author: navanithans
---

# Code Review Protocol

## <critical_constraints>
1. ❌ **NO** commands. Frame suggestions as questions ("Why not use X?" vs "Use X").
2. ❌ **NO** unexplained changes. Explain *why* it improves code.
3. ✅ **MUST** prioritize Critical (Bugs/Security) > Style.
4. ✅ **MUST** use `assets/report_template.md`.
5. ✅ **MUST** be constructive.
</critical_constraints>

## <process>
1. **Context**: identify language, framework, purpose.
2. **<thinking> Deep Scan**:
   - Check against `assets/checklist.md`.
   - **Correctness**: Logical flaws, null checks, race conditions.
   - **Security**: Injection, XSS, Secrets.
   - **Performance**: Big O, N+1 queries, leaks.
   - **Style**: Naming, idioms.
   </thinking>
3. **Draft Report**:
   - Group by severity.
   - Include Location, Problem, Suggested Fix.
4. **<validation_gate>**:
   - Check tone. Ensure critical issues have fixes.
   - Run validation script.
   </validation_gate>
5. **Final Output**: Present Markdown report.
</process>

## <templates>
See `assets/report_template.md`.
</templates>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/navanithans) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
