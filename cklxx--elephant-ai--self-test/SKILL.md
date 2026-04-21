---
name: self-test
description: When Lark integration tests need running → execute test suite, analyze failures, auto-iterate fixes by severity. Use when this capability is needed.
metadata:
  author: cklxx
---

# self-test

Run the Lark scenario test suite, analyze failures by root cause (test_drift, prompt_issue, tool_bug, etc.), apply tiered auto-fixes (up to 3 rounds), and generate a structured report. All test execution, failure analysis, and iterative repair logic are handled by run.py.

## 调用

```bash
python3 skills/self-test/run.py run
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cklxx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
