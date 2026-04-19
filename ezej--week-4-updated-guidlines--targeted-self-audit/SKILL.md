---
name: targeted-self-audit
description: Run a post-generation self-audit tailored to the dominant failure mode (loopholes, fake precision, imported constraints, too slow/too many questions). Use when you need a reliable “second pass” that actually catches the common ways prompting guidelines backfire. Use when this capability is needed.
metadata:
  author: ezej
---

# Targeted Self Audit

## Choose one scan

### 1) Loophole scan (safety/compliance/access control)
Check:
- Did any rewrite weaken a prohibition into a “best effort”?
- Are side channels covered? (timing, error messages, metadata)
- Do any requirements leak bypass details?
Action:
- Strengthen at least 2 Statements to close the biggest loopholes.

### 2) Fake-precision scan (early discovery)
Check:
- Did you invent numeric targets without baselines or measurement plans?
- Did you claim “fairness/empathetic/trustworthy” without defining them?
Action:
- Replace unjustified numbers with an operationalization plan (rubric/audit/study/TODO metric plan).

### 3) Imported-constraint scan (few-shot examples)
Check:
- Did you import time windows, ID checks, refund policies, or other constraints not in the problem statement?
Action:
- Remove them or explicitly mark as assumption (and flag as risky).

### 4) Too-slow scan (incidents)
Check:
- Did you ask too many questions and avoid action?
- Is the mitigation plan actionable in the next 2 hours?
Action:
- Compress to the 5 most decision-critical questions; provide a plan with confidence labels.

## Prompt snippet (copy/paste)

```text
After producing the output, run a targeted self-audit:
Scan type: <loophole|fake-precision|imported-constraint|too-slow>.
List what you found and revise the output to address the top issues.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ezej) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
