---
name: output-formatting
description: Guidelines for formatting agent responses for Telegram display Use when this capability is needed.
metadata:
  author: iliagerman
---

# Output Formatting Guide

This skill provides formatting guidelines for Telegram messages. Follow these rules to ensure your responses display correctly.

## General Principles

1. **Keep it concise** - Telegram users expect quick, scannable responses
2. **Use plain text primarily** - Complex formatting often breaks
3. **Avoid raw markdown tables** - They don't render in Telegram
4. **No HTML tags** - Telegram uses its own markup
5. **Limit response length** - Keep under 4000 characters when possible

## Text Formatting

### Supported Formatting
- **Bold**: Use `*text*` for emphasis (sparingly)
- **Italic**: Use `_text_` for secondary emphasis
- **Code**: Use `` `code` `` for inline code, commands, or technical terms
- **Code blocks**: Use triple backticks for multi-line code

### Avoid
- Headers (`#`, `##`, `###`) - Not supported, use bold instead
- Horizontal rules (`---`) - Not rendered
- Complex nested formatting

## Tables - IMPORTANT

**Telegram does NOT render markdown tables.** Never output raw markdown tables like:

```
| Column 1 | Column 2 |
|----------|----------|
| Data     | Data     |
```

### Instead, use these alternatives:

#### Option 1: Numbered List (Preferred for most data)
```
📧 Your emails:

1. *Adobe Creative Cloud*
   New ways to organize your Lightroom library
   Jan 29, 16:14

2. *Google*
   Security alert
   Jan 29, 12:40
```

#### Option 2: Emoji-prefixed lines (Good for key-value data)
```
📊 Account Summary:
• Balance: $1,234.56
• Transactions: 42
• Last activity: Jan 29
```

#### Option 3: Compact inline format (For simple lists)
```
Files: report.pdf (2.1MB), data.csv (156KB), notes.txt (4KB)
```

#### Option 4: Aligned text blocks (For structured data)
```
Name         Status    Size
─────────────────────────
report.pdf   ✅ Done   2.1MB
data.csv     ⏳ Pending 156KB
notes.txt    ✅ Done   4KB
```

## Lists

### Bullet Lists
Use simple bullets or emojis:
```
• Item one
• Item two
• Item three
```

Or with emojis for visual distinction:
```
📁 Documents
📷 Photos  
🎵 Music
```

### Numbered Lists
```
1. First step
2. Second step
3. Third step
```

## Emojis for Visual Structure

Use emojis to replace headers and add visual hierarchy:

- 📧 Email/Messages
- 📁 Files/Folders
- ✅ Success/Complete
- ❌ Error/Failed
- ⚠️ Warning
- ℹ️ Information
- 🔍 Search results
- 📊 Data/Statistics
- 🕐 Time/Schedule
- 📍 Location
- 🔗 Links
- 💡 Tips/Suggestions

## Response Structure Template

For most responses, follow this pattern:

```
[Emoji] Brief title or summary

[Main content - list, data, or explanation]

[Optional: Next steps or questions]
```

Example:
```
📧 You have 5 unread emails:

1. *Adobe* - New Lightroom features (16:14)
2. *Google* - Security alert (12:40)
3. *No-IP* - DNS plan reminder (14:21)

Reply with a number to read that email, or "all" for details.
```

## Charts and Visualizations

Telegram cannot display charts. Use text-based alternatives:

### Progress bars
```
Download: [████████░░] 80%
```

### Simple bar charts
```
Sales by Region:
US     ████████████ 45%
EU     ████████ 30%
Asia   ██████ 25%
```

### Sparkline-style trends
```
Last 7 days: ▁▂▄▆█▇▅ (trending up)
```

## Error Messages

Format errors clearly:
```
❌ Could not complete request

Reason: File not found at specified path
Suggestion: Check the filename and try again
```

## Long Content

For content exceeding ~4000 characters:
1. Summarize first, offer details on request
2. Split into logical sections
3. Use "Reply 'more' for additional results"

## Code Output

Always use code blocks for:
- Command output
- File contents
- JSON/XML data
- Error logs

```
$ ls -la
total 24
drwxr-xr-x  5 user  staff   160 Jan 29 10:00 .
-rw-r--r--  1 user  staff  1234 Jan 29 09:55 file.txt
```

## Summary Checklist

Before sending a response, verify:
- [ ] No markdown tables (use lists instead)
- [ ] No headers (use bold + emoji)
- [ ] Under 4000 characters
- [ ] Clear visual hierarchy with emojis/bullets
- [ ] Code in proper code blocks
- [ ] Actionable next steps when appropriate

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iliagerman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
