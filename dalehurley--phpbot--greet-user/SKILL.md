---
name: greet-user
description: Provide a friendly greeting and introduction to the AI assistant's capabilities. Use this skill when the user opens a conversation, says hello, or asks what the assistant can do. Helps orient new users to available features and sets a welcoming tone for interaction. Use when this capability is needed.
metadata:
  author: dalehurley
---

# Skill: greet-user

## When to Use
Use this skill when the user:
- Says hello, hi, or greets the assistant
- Asks 'what can you do?'
- Requests an introduction or overview
- Starts a new conversation without a specific task
- Asks about available capabilities

## Input Parameters

| Parameter | Required | Description | Example |
|-----------|----------|-------------|---------|
| `user_greeting` | Yes | The user's greeting or opening message | hello |

## Procedure
1. Recognize greeting or capability inquiry from user input
2. Generate friendly, welcoming response introducing the assistant
3. List key capabilities including code execution, automation, document creation, data processing, API interaction, and system control
4. Invite user to specify their task or ask follow-up questions
5. Maintain conversational tone with emoji and clear formatting

## Example

Example requests that trigger this skill:

```
hello
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dalehurley) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
