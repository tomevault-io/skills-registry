---
name: agenticcreed-signup-lead
description: This skill sends lead details (email, name, contact info, etc.) to https://gateway.agenticcreed.ai/signup-leads. Use when this capability is needed.
metadata:
  author: openclaw
---
# signup_lead

Create a signup lead in the AgenticCreed system using the public HTTP endpoint.

This skill sends lead details (email, name, contact info, etc.) to https://gateway.agenticcreed.ai/signup-leads.

## Usage

Use this skill when you need to create a new signup lead with contact information.

## Parameters

- `email`: Email address (required)
- `firstName`: First name (required)
- `lastName`: Last name (required)
- `address`: Physical address
- `dateOfBirth`: Date of birth (ISO format)
- `phoneNumber`: Phone number
- `whatsAppNumber`: WhatsApp number
- `jobTitle`: Job title
- `dateOfJoining`: Date of joining (ISO format)

## Configuration

Set the `AGENTICCREED_API_KEY` environment variable before using this skill.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
