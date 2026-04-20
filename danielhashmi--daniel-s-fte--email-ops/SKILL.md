---
name: email-ops
description: WHAT: Send emails, check sent items, and manage drafts via Gmail integration. WHEN: User says 'send email', 'draft reply', 'check sent emails'. Trigger on: communication tasks, client response, email automation. Use when this capability is needed.
metadata:
  author: danielhashmi
---

# Email Operations

## When to Use
- Sending approved emails to clients or contacts
- Drafting responses to inquiries
- Verifying sent emails
- Managing email templates

## Instructions
1. **Send Email**:
   ```bash
   python3 .claude/skills/email-ops/scripts/main_operation.py --action send --to "RECIPIENT" --subject "SUBJECT" --body "BODY"
   ```
   *Note: Requires approval for new contacts. Use `--attachment "PATH"` for attachments.*

2. **List Recent Sent**:
   ```bash
   python3 .claude/skills/email-ops/scripts/main_operation.py --action list-sent --limit 5
   ```

3. **Check Status**:
   ```bash
   python3 .claude/skills/email-ops/scripts/main_operation.py --action status
   ```

## Validation
- [ ] Email sent successfully (or logged in dry-run)
- [ ] Audit log entry created
- [ ] Error logged if failure occurs

See [REFERENCE.md](./REFERENCE.md) for detailed credentials setup.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielhashmi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
