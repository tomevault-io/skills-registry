---
name: start-challenge
description: Starts a challenge playground session. Use when you need to launch a challenge for testing or solving. Use when this capability is needed.
metadata:
  author: iximiuz
---

Start the challenge `$0`.

Run the following command in the background (it may block waiting for init tasks):

```sh
labctl challenge start $0 --no-open --no-ssh
```

The `--no-open` flag prevents opening the browser.
The `--no-ssh` flag prevents starting an interactive SSH session.

Since this command may take a long time (it waits for all init tasks to complete),
run it in the background and immediately use the `list-running-playgrounds` skill
to find the playground ID for further operations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iximiuz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
