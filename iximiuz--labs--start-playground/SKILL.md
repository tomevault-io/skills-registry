---
name: start-playground
description: Starts a new playground session and returns its ID. Use when you need to spin up a sandbox environment for testing or debugging. Use when this capability is needed.
metadata:
  author: iximiuz
---

Start a new playground session for playground `$0`.

Run the following command:

```sh
labctl playground start $0 --quiet --skip-wait-init
```

The `--quiet` flag makes the command print only the playground ID.
The `--skip-wait-init` flag avoids blocking indefinitely waiting for init tasks to complete
(useful because init tasks may fail during debugging).

Capture and report the returned **playground ID** - it is needed for all subsequent
playground operations (SSH, running commands, checking tasks).

After starting, use the `list-running-playgrounds` skill or `labctl playground list`
to verify the playground is running.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iximiuz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
