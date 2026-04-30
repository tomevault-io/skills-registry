---
name: fix-test
description: Can you check out branch "{{ BRANCH_NAME }}", and run {{ TEST_COMMAND_TO_RUN }}. Use when this capability is needed.
metadata:
  author: ranbot-ai
---


Can you check out branch "{{ BRANCH_NAME }}", and run {{ TEST_COMMAND_TO_RUN }}.

Help me fix these tests to pass by fixing the {{ FUNCTION_TO_FIX }} function in file {{ FILE_FOR_FUNCTION }}.

PLEASE DO NOT modify the tests by yourself -- Let me know if you think some of the tests are incorrect.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ranbot-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
