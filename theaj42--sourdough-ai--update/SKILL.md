---
name: update
description: Save what you worked on so your AI can pick it up next time Use when this capability is needed.
metadata:
  author: theaj42
---

# Session Update

Run this when you're finishing a work session, or at natural breakpoints during longer sessions.

**Invocation**: `/update` or "Let's save our progress"

## Instructions

When this skill is invoked, follow these steps:

### 1. Get Current Date and Time

Use the system date to determine:
- Today's date (for the log filename): `YYYY-MM-DD`
- Current time (for the session entry): `HH:MM` (24-hour format)

### 2. Create or Update Today's Session Log

Session logs live in `~/ai-data/logs/sessions/YYYY-MM-DD.md`.

**If the file doesn't exist**, create it with this structure:

```markdown
# Session Log - YYYY-MM-DD

## Summary
**Major Accomplishments**:
- (To be filled)

## Next Actions
- (To be filled)

---

## Sessions

### HH:MM - [Brief description]
- What we worked on
- Key decisions made
- Files created or modified
- Open questions or next steps
```

**If the file exists**, add a new session entry after the `---` separator, before previous sessions (reverse chronological order).

### 3. Capture the Session Content

Document:
- **What was worked on** - tasks, problems, topics discussed
- **Key decisions** - choices made and why
- **Concrete outcomes** - files changed, code written, problems solved
- **Open items** - things to pick up next time

Be specific but concise. Future-you needs enough detail to remember context.

### 4. Update the Summary Section

Add or update the summary at the top of the file to reflect today's accomplishments.

### 5. Update Next Actions (if applicable)

If there are clear next steps, add them to the Next Actions section.

### 6. Capture Observations (Optional)

If you noticed something about how the user prefers to work:
- Add it to `~/ai-data/learning/user_model.yaml`
- Use the format from `~/ai-data/learning/templates/user_model_template.yaml`

Only capture genuine observations, not every interaction.

### 7. Confirm Completion

Brief acknowledgment: "Session logged." or "Saved to today's log."

---

## Example Session Entry

```markdown
### 14:30 - Debugging API timeout issue
- Investigated timeout errors in production logs
- Root cause: Connection pool exhaustion under load
- Increased pool size from 10 to 50 in config/database.yml
- Deployed to staging, monitoring for 30 minutes
- TODO: Deploy to production if staging looks good
```

---

## Customization Ideas

As you get comfortable, you might add:
- Reflection prompts ("What worked well?")
- Automatic git commits of the session log
- Cross-posting to daily notes or task systems

These are enhancements - the core skill works without them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theaj42) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
