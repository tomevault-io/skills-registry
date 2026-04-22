---
name: client-update
description: Send professional client updates with deliverables via email and WhatsApp notification. Use when user wants to share work, send assets, or give a client an update on progress. Use when this capability is needed.
metadata:
  author: hermeticormus
---

# Client Update Skill

Standardized workflow for sending professional client updates with deliverables.

## When This Skill Activates

- User says "send this to the client"
- User wants to share deliverables or assets
- User requests a client update
- User mentions sending work to Cynthia or other clients

## Workflow Steps

### 1. Identify Client & Project

Check Core Memory for client contact information:
```
Client: Cynthia DeBriey
Email: cynthia.debriey@gmail.com
WhatsApp: +507 6380-7634 (50763807634@c.us)
Project: FloreSer / Philips Day
```

### 2. Prepare Deliverables

```bash
# Create dated deliverable folder
mkdir -p ~/projects/01-ACTIVE/[PROJECT]/client/deliverables/YYYY-MM-DD_description/

# Zip files if needed
zip -r deliverable.zip [files]

# Copy to assets-shared for tracking
cp deliverable.zip ~/projects/01-ACTIVE/[PROJECT]/client/assets-shared/
```

### 3. Compose Email

Structure:
- **Greeting**: Friendly, professional
- **Summary**: What was done (1-2 sentences)
- **Context**: Why this matters
- **Contents**: What's in the attachment
- **Status**: Where we are (done/in progress/needs feedback)
- **Ask**: What you need from them
- **Sign-off**: Warm closing

### 4. Send Email (Gmail)

```python
mcp__gmail__send_email(
    to=["client@email.com"],
    subject="[Project] - Deliverable Description",
    body="...",
    attachments=["path/to/file.zip"]
)
```

### 5. Send WhatsApp Notification

```python
mcp__periskope-whatsapp__periskope_send_message(
    phone="[number]@c.us",
    message="Quick note - just sent you an email with [description]. Check your inbox!"
)
```

### 6. Log Communication

Update the project's `client/CLIENT-WORKFLOW.md` communication log table.

### 7. Save to Core Memory

Ingest the communication for future reference.

## Project-Specific Configs

### FloreSer / Philips Day
- **Client folder**: `~/projects/01-ACTIVE/FloreSer/client/`
- **Contact**: Cynthia DeBriey
- **Email**: cynthia.debriey@gmail.com
- **WhatsApp**: 50763807634@c.us

## Error Handling

- **Gmail auth error**: Alert user to re-authenticate, offer to draft email instead
- **WhatsApp error**: Provide manual message to copy
- **Large files (>25MB)**: Suggest Google Drive link or split files

## Example Usage

User: "Send these assets to the client"

Claude:
1. Zips assets
2. Copies to client/assets-shared/
3. Sends email with summary
4. Sends WhatsApp notification
5. Updates communication log
6. Confirms completion

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hermeticormus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
