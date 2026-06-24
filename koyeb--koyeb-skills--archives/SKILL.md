---
name: archives
description: Create Koyeb archives for deployments. Use when the user needs to upload or create an archive for a service deployment. Use when this capability is needed.
metadata:
  author: koyeb
---

# Koyeb Archives

## When to use

Use this skill to create archives that can be referenced by deployments.

## Prerequisites

- Koyeb CLI installed and on PATH
- Authenticated session (see references/koyeb-auth.md)

## Workflow

1. Identify the source directory or file to archive.
2. Create the archive with the CLI.
3. Use the resulting archive ID in a service deploy step.

## Commands

- Create archive: `koyeb archives create <name> [flags]`

## Examples

- Create an archive from a directory: `koyeb archives create ./dist --ignore-dir node_modules,build`

## References

For detailed flags, see references/koyeb-archives-flags.md.

- references/koyeb-cli.md
- references/koyeb-archives-flags.md
- references/koyeb-auth.md
- references/koyeb-output.md
- scripts/koyeb-cli.sh

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/koyeb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
