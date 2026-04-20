---
name: brightwheel-send-message
description: Sends messages through Brightwheel's web messaging interface. Use when this capability is needed.
metadata:
  author: carlosmsanchezm
---

# Brightwheel Send Message

Send messages through Brightwheel's web messaging interface using browser automation.

## Prerequisites
- Must be logged into Brightwheel (use `brightwheel_login_helper` first)
- Message must be pre-approved (Tier 1 auto-approved or Tier 2 provider-approved)

## Workflow

1. **Verify login state** — navigate to Brightwheel, check if session active
2. **Navigate to messaging** — go to the messaging section
3. **Select recipient** — find the correct parent/classroom
4. **Compose message** — enter the pre-approved message text
5. **Screenshot before send** — capture for verification
6. **Phase A: Pause for confirmation** (current behavior)
   - Report to requesting agent: "Message composed in Brightwheel. Ready to send."
   - Wait for final go-ahead
7. **Send** — click send button
8. **Screenshot after send** — capture confirmation
9. **Report success** — log to case system

## Navigation Steps

```
1. Go to: https://schools.mybrightwheel.com/messages
2. Click "New Message" or find existing thread
3. Search for recipient by name
4. Type message in compose box
5. [Phase A: Pause here]
6. Click Send
```

## Safety Checks
- Verify recipient name matches the intended recipient before composing
- Verify message content matches the approved draft
- Never send to "All Parents" without explicit provider approval
- Take screenshots at every step for audit trail

## Error Recovery
- If navigation fails → retry once, then report error
- If recipient not found → report to requesting agent with available options
- If send button is not clickable → screenshot and report

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carlosmsanchezm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
