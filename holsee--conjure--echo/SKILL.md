---
name: echo
description: | Use when this capability is needed.
metadata:
  author: holsee
---

# Echo Skill

A minimal skill that echoes messages back. Perfect for learning how Conjure works.

## Usage

To echo a message, run the echo script:

```bash
python3 scripts/echo.py "Your message here"
```

The script will print the message back with a timestamp.

## Examples

```bash
# Simple echo
python3 scripts/echo.py "Hello, World!"
# Output: [2024-01-15 10:30:00] Echo: Hello, World!

# Multi-word message
python3 scripts/echo.py "This is a test message"
# Output: [2024-01-15 10:30:01] Echo: This is a test message
```

## How It Works

1. The script receives a message as a command-line argument
2. It formats the message with a timestamp
3. It prints the formatted message to stdout

This simple flow demonstrates the core pattern of all Conjure skills:
- Claude reads the SKILL.md to understand capabilities
- Claude uses `bash_tool` to execute scripts
- Results are returned to continue the conversation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/holsee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
