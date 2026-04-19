---
name: man-config
description: Reads man pages to find configuration options. Use when the user asks to configure an application, needs to find a config option, or asks "how do I make X do Y" for a CLI tool or system program. Use when this capability is needed.
metadata:
  author: austinpray
---

# Man Page Configuration Helper

## Instructions

1. Identify the program or system being configured
2. Read the relevant man page(s) using `man <program>` or `man <section> <program>`
3. Search for the specific feature or option requested
4. Find configuration file location and syntax
5. Apply the configuration

## Common man page sections

- `man 1 <cmd>` - User commands
- `man 5 <config>` - File formats and config files (e.g., `man 5 sway`)
- `man 7 <topic>` - Miscellaneous (conventions, protocols)
- `man 8 <cmd>` - System administration commands

## Tips

- Many programs have both a command man page and a config file man page (e.g., `sway(1)` and `sway(5)`)
- Use `man -k <keyword>` to search for relevant man pages
- Pipe man output through `grep` to find specific options
- Check "SEE ALSO" section for related documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/austinpray) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
