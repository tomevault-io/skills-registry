---
name: gmail-unsubscribe-cleaner
description: Build and operate an agentic Gmail unsubscribe-and-clean system. Use when tasks involve Gmail email reading, marketing/promotions detection, List-Unsubscribe handling, unsubscribe automation, inbox cleanup, storage reduction, or safety-gated delete/archive workflows. Use when this capability is needed.
metadata:
  author: alishas3-singh
---

# Gmail Unsubscribe Cleaner

## Quick start
1. Confirm explicit consent to read and modify Gmail.
2. Collect policy inputs: allowlist, denylist, cleanup action, dry-run limit, and schedule.
3. Pull Gmail message metadata and convert it to JSONL with `scripts/gmail_export_metadata.py` (see `references/data-schema.md`).
4. Run `scripts/classify_marketing.py` to label marketing messages.
5. Present a dry-run summary and ask for approval before unsubscribing or trashing.
6. Apply unsubscribe actions and cleanup steps, then log outcomes.

## Safety rules
- Require a dry-run before any destructive action.
- Skip protected categories unless the user opts in.
- Use least-privilege Gmail scopes and avoid storing raw message bodies.
- Keep an audit log and add a durable label to processed messages.

## Resources
- `scripts/gmail_export_metadata.py`: Gmail API metadata exporter (OAuth).
- `scripts/classify_marketing.py`: Offline classifier for marketing signals.
- `scripts/gmail_unsubscribe_oneclick.py`: List-Unsubscribe one-click executor.
- `scripts/gmail_unsubscribe_mailto.py`: Mailto unsubscribe sender (approval gated).
- `scripts/gmail_apply_cleanup.py`: Archive/trash/label cleanup actions.
- `references/workflow.md`: End-to-end agentic workflow.
- `references/policy.md`: Classification policy and allowlist guidance.
- `references/data-schema.md`: Input/output schema for classifier.
- `references/cleanup-actions.md`: Cleanup options and space recovery notes.
- `references/gmail-api-setup.md`: OAuth setup and scope guidance.
- `assets/allowlist.txt`: Allowlist template for trusted senders.
- `assets/denylist.txt`: Denylist template for always-unsubscribe senders.

## Output expectations
- Provide a summary: total scanned, marketing candidates, unsubscribe candidates, cleanup actions.
- Provide a confirmation step before changes.
- Produce an action log for transparency and rollback.
- If trashing, send a summary email after approval.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alishas3-singh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
