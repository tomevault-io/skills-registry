---
name: open-application
description: Open or launch applications on your computer by name. Use this skill when the user asks to open, launch, or start an application like Mail, Finder, Safari, Chrome, or any other installed macOS application. Works with both built-in and third-party applications. Use when this capability is needed.
metadata:
  author: dalehurley
---

# Skill: open-application

## When to Use
Use this skill when the user asks to:
- Open an application (Mail, Safari, Finder, etc.)
- Launch a program
- Start an app
- Bring an application to the foreground
- Open a specific macOS application by name

## Input Parameters

| Parameter | Required | Description | Example |
|-----------|----------|-------------|---------|
| `application_name` | Yes | The name of the macOS application to open (e.g., Mail, Safari, Finder, Chrome, Slack) | Mail |

## Procedure
1. Extract the application name from the user's request
2. Validate that the application name is clear and unambiguous (use ask_user if needed for clarification)
3. Execute the open command: open -a {{APPLICATION_NAME}}
4. Wait 2-3 seconds for the application to launch
5. Confirm to the user that the application has been opened or brought to the foreground

## Reference Commands

Commands from a successful execution (adapt to actual inputs):

```bash
open -a Mail
```

Replace `{{PLACEHOLDER}}` values with actual credentials from the key store.

## Example

Example requests that trigger this skill:

```
control my computer to open mail
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dalehurley) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
