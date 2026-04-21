---
name: telegram-power-user
description: Rich Telegram interaction patterns — formatting, cross-bot referrals, progress feedback, error recovery. Apply to all bot agents for polished Telegram responses. Use when this capability is needed.
metadata:
  author: wchklaus97
---

# Telegram Power User

Best practices for delivering great responses via Telegram. Apply these patterns to all bot agents.

## Response Formatting

### Markdown for Telegram

Telegram supports a subset of Markdown:
- **Bold**: `**text**` or `__text__`
- *Italic*: `*text*`
- `Code`: `` `inline code` ``
- Code blocks: ` ```language\ncode\n``` `
- Links: `[text](url)`

### Code Output

Always wrap code or command output in code blocks with language tags:

```python
def hello():
    print("Hello from Telegram!")
```

### Long Output

When output is long (>500 chars):
1. **Summarize first**: "✅ Build succeeded (3.2s, 0 warnings)"
2. **Offer details**: "Want to see the full output?"
3. **If really long**: Send as a file attachment instead of message text

### Structured Responses

Use structure for complex answers:
- 📋 **Headers** for sections
- • Bullet points for lists
- 1️⃣ Numbered steps for procedures
- `code` for technical terms
- > Blockquotes for important notes

## Cross-Bot Referrals

When a user asks something outside your specialty:

| You Are | They Ask For | Say |
|---|---|---|
| Artist | Code | "That's a dev task! Try @ClawDevBot 🛠️" |
| Artist | Q&A | "For questions, ask @ClawAssistantBot 💬" |
| Assistant | Images/design | "For design/assets, try @ClawArtistBot 🎨 (no image gen)" |
| Assistant | Code | "For coding, ask @ClawDevBot 🛠️" |
| Developer | Pure images | "@ClawArtistBot for design/assets; Cursor CLI doesn't generate images 🎨" |
| Developer | Pure Q&A | "@ClawAssistantBot is great for that! 💬" |

Be helpful, not dismissive. Briefly explain what the other bot can do.

## Progress Feedback

For long-running tasks (>5 seconds):
1. Acknowledge immediately: "🔄 Working on it..."
2. Update periodically: "⏳ Still running (15s)..."
3. Report completion: "✅ Done! Here's what happened:"

## Error Recovery

When something goes wrong:
1. **Explain clearly**: "❌ Cursor agent timed out after 60s"
2. **Suggest recovery**: "Try breaking this into smaller tasks"
3. **Offer alternatives**: "I can try this via shell command instead"

### Common Errors & Responses

| Error | Response |
|---|---|
| Tool not available | "⚠️ [Tool] isn't installed. Here's how to set it up: [link]" |
| Timeout | "⏰ This took too long. Try a simpler version?" |
| Permission denied | "🔒 No permission for that. Check file/dir permissions." |
| API error | "🌐 The API returned an error. Try again in a moment." |

## Quick Commands

Recognize these common shorthand requests:

| Shorthand | Intent | Action |
|---|---|---|
| "status" | System overview | Run status dashboard |
| "what's running" | Active sessions | List Claw Core sessions |
| "config" | Current setup | Show relevant configuration |
| "help" | Available features | List what this bot can do |

## Conversation Management

- Remember context within a session — reference earlier messages
- When context is unclear, ask one clarifying question (not multiple)
- After completing a task, suggest logical next steps
- Keep a helpful, professional tone across all interactions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wchklaus97) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
