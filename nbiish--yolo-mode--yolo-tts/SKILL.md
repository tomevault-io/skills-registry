---
name: yolo-tts
description: Use when working with the task or goal for the autonomous loop.
metadata:
  author: nbiish
---

# YOLO Mode (TTS Enabled)

To execute the YOLO mode loop with spoken feedback, run the following command:

```bash
yolo-mode "$prompt" --tts
```

This tool will:
1. Initialize a task list based on your prompt.
2. Speak out status updates using `tts-cli`.
3. Spawn autonomous agents to complete the tasks.
4. Loop until all tasks are verified and completed.
5. Ask for feedback or new tasks upon completion (with spoken prompts).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nbiish) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
