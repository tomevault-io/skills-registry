---
name: outreach-personalizer
description: This skill generates hyper-personalized outreach sequences for IT hiring managers. Use when this capability is needed.
metadata:
  author: jessie-rgb
---

# Stratice Outreach Personalizer

## Goal
Generate hyper-personalized outreach sequences for IT/Engineering hiring managers based on their LinkedIn profile, company signals, and Stratice's unique value prop.

## Instructions
1. Analyze the provided `linkedin_url` and `company_url`.
2. Identify a "hook" (recent promotion, common connection, company news, or tech stack signal).
3. Draft a 3-step sequence:
   - **Step 1:** LinkedIn InMail (Under 100 words, soft ask).
   - **Step 2:** Personalized Email (Value-led, mentions Stratice's specific candidate pool).
   - **Step 3:** Phone Script / Voicemail (Concise, focusing on "speed to market").
4. Tone: Consultative, professional, and peer-to-peer. Avoid "recruiter-speak."

## Inputs
- `linkedin_url`: The prospect's LinkedIn profile.
- `company_url`: The hiring manager's company website.
- `notes`: (Optional) Specific context or req details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jessie-rgb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
