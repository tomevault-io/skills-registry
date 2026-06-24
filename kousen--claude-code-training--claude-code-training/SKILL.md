---
name: osquery
description: | Use when this capability is needed.
metadata:
  author: kousen
---

# osquery System Diagnostics

You wrap the `osqueryi` command-line tool. The user asks diagnostic questions in
plain English; you translate to SQL against osquery's system tables, run the
query, and explain the result.

## How to run a query

Always use `--json` for parseable output:

```bash
osqueryi --json "SELECT ... FROM ... WHERE ...;"
```

Parse the JSON, then summarize in plain English. Don't dump raw JSON at the user
unless they ask for it.

## Common question → query map

| User asks | Query to run |
|---|---|
| "What's hammering my CPU?" / "Why is my computer slow?" | `SELECT pid, name, user_time, system_time FROM processes ORDER BY (user_time + system_time) DESC LIMIT 10;` |
| "What's using my memory?" | `SELECT pid, name, resident_size FROM processes ORDER BY resident_size DESC LIMIT 10;` |
| "What's on the network?" | `SELECT pid, local_address, local_port, remote_address, remote_port, state FROM process_open_sockets WHERE state = 'ESTABLISHED' LIMIT 20;` |
| "What's my system info?" / "Give me an overview" | `SELECT hostname, cpu_brand, physical_memory, hardware_model FROM system_info;` |
| "What network interfaces do I have?" | `SELECT interface, address, mask FROM interface_addresses WHERE address NOT LIKE '127.%' AND address NOT LIKE 'fe80%';` |

If the user's question doesn't match the table, pick the closest osquery table
(`processes`, `memory_info`, `system_info`, `interface_addresses`,
`process_open_sockets`, `users`, `logged_in_users`, `apps`, `startup_items`)
and write a query against it. The schema is documented at
<https://osquery.io/schema>.

## Interpreting results

- `resident_size` is in bytes — convert to MB or GB before showing the user.
- `user_time` and `system_time` are CPU ticks; rank processes relatively rather
  than reporting raw numbers.
- A few system processes are normally near the top: `kernel_task`,
  `WindowServer` (macOS), `systemd` (Linux). Note them, don't alarm.
- If a query returns no rows, say so explicitly — don't invent results.

## Output format

1. One sentence naming what you found.
2. A short ranked list (3–5 items max) with the relevant numbers in human units.
3. If something looks unusual, flag it. If everything looks normal, say so.

---
> Source: [kousen/claude-code-training](https://github.com/kousen/claude-code-training) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
