---
name: create-ticket
description: Creates new ticket with templates and knowledge refs
metadata:
  author: sato-dev1234
---

# /create-ticket

Creates new ticket with templates and knowledge refs.

## Progress Checklist

```
- [ ] Step 1: Auto-numbering with atomic acquisition
- [ ] Step 2: Confirm/modify TICKET_ID
- [ ] Step 3: Validate
- [ ] Step 4: Gather requirements
- [ ] Step 5: Parse requirements
- [ ] Step 6: Invoke ticket-writer
- [ ] Step 7: Output
```

## Steps

1. Auto-numbering with atomic acquisition (if TICKET_ID not provided):
   ```bash
   node ~/.claude/scripts/next-ticket-id.js --atomic "$TICKETS_ON_CREATE"
   ```
   - Uses mkdir atomicity to prevent race conditions
   - Reads config from environment variables ($TICKETS_DIR, $TICKETS_PREFIX, $TICKETS_DIGITS)
   → GENERATED_ID

2. AskUserQuestion: confirm/modify TICKET_ID

3. Validate:
   - Pattern check: starts with $TICKETS_PREFIX, correct digit count
   - Duplicate check via ticket-reader list
   - If invalid → error, retry

4. Gather requirements:
   - If REQUIREMENTS provided in args → use it
   - Else → AskUserQuestion for requirements

5. Parse requirements:
   - Extract AC items
   - Extract context
   - Identify edge cases

6. Invoke ticket-writer:
   ```
   OPERATION=create
   TICKET_ID=$TICKET_ID
   REQUIREMENTS=$REQUIREMENTS
   ```
   - ticket-writer self-loads templates from ~/.claude/skills/create-ticket/templates/
   - ticket-writer reads $TICKETS_ON_CREATE from environment

7. Output: TICKET_ID, TICKET_PATH, END

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sato-dev1234) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
