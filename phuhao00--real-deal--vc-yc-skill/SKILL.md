---
name: vc-yc-skill
description: VC/YC fundraising module for real_deal platform including investor profiles, pitch pages, deal rooms (data rooms), fundraising matching, and dual-consent introductions. Use when building investor features, creating pitch pages, managing deal rooms, implementing fundraising workflows, or connecting founders with investors. Use when this capability is needed.
metadata:
  author: phuhao00
---

# VC/YC Fundraising Module

## Core Features

### Investor Profiles
- Investment focus (stage, industry, geography)
- Portfolio companies
- Booking availability (time slots)
- Investment criteria (min/max check size)
- Contact preferences

### Pitch Pages
- Founder/company presentation
- Company metrics and traction
- Fundraising goals (amount, stage)
- Team information
- Product demo/media
- Private data access controls

### Deal Rooms
- Private data room for due diligence
- Watermarked document sharing
- NDA signing workflow (optional)
- Access audit logs
- Document versioning

## Workflows

### Fundraising Process
1. **Setup**: Founder creates pitch page + deal room
2. **Discovery**: Investors discover opportunities
3. **Engagement**: Book time slots, request access
4. **Due Diligence**: Access deal room with controls
5. **Matching**: AI-powered recommendations (explainable)
6. **Introduction**: Dual-consent introductions between parties
7. **Closing**: Track deal status, updates

### Investor Discovery
- Browse pitch pages by filters
- AI-recommended matches (explainable, opt-out)
- Batch application for multiple opportunities
- Saved searches and alerts

### Founder Discovery
- Browse investor profiles
- View investment criteria
- Book available time slots
- Request introductions

## Data Models

### Investor
- `InvestorProfile` - Investor information
- `TimeSlot` - Availability calendar
- `InvestmentCriteria` - Focus areas

### Fundraising
- `PitchPage` - Founder presentation
- `DealRoom` - Private data room
- `DocumentAccess` - Document access control
- `IntroductionRequest` - Dual-consent requests

## Access Controls

### Deal Room Security
- Role-based access (founder, investor, advisor)
- Watermarked documents
- NDA enforcement (optional)
- Expiring access links
- Audit trail for all downloads/views

### Pitch Page Privacy
- Public listing vs. private
- Password-protected access
- Investor whitelist
- Visibility tiers

## AI Matching

### Explainable Recommendations
- Clear matching rationale (industry, stage, geography)
- "Why this match?" explanation
- Opt-out of recommendations
- Fine-tune matching parameters

### Batch Operations
- Investors can batch apply to multiple deals
- Founders can batch send to multiple investors
- Manage response rates

## Common Tasks

### Create Pitch Page
1. Founder submits pitch data
2. Upload media assets (logo, screenshots, videos)
3. Set fundraising goals and metrics
4. Configure privacy settings
5. Publish to marketplace (or keep private)

### Set Up Deal Room
1. Create `DealRoom` for pitch
2. Upload due diligence documents
3. Configure access controls
4. Set up NDA workflow (optional)
5. Grant access to specific investors

### Match Investor to Deal
1. Run AI matching algorithm
2. Generate explainable recommendations
3. Present matches to both parties
4. Track interest indicators
5. Facilitate introduction request

### Manage Introduction
1. Founder requests introduction
2. Investor receives request + dual-consent
3. Both parties accept → introduction made
4. Track follow-up and status
5. Update deal room access as needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuhao00) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
