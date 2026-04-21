---
name: compliance-skill
description: Company verification and job/content compliance system for real_deal platform including KYB (Know Your Business) verification, job posting compliance, content moderation, and regional compliance. Use when implementing company verification workflows, job posting reviews, content moderation, or ensuring regulatory compliance. Use when this capability is needed.
metadata:
  author: phuhao00
---

# Company Verification & Compliance

## Company Verification (KYB)

### Verification Requirements
- Business license verification
- Domain ownership proof
- Corporate email verification
- Physical address verification (optional)

### Verification Levels
- **Basic**: Email verification only
- **Standard**: License + email
- **Premium**: Full KYB with address verification

### Verification Flow
1. Company submits verification documents
2. System validates documents (AI + rules)
3. Manual review queue for edge cases
4. Grant/deny with feedback
5. Appeal process available

## Job Posting Compliance

### Structured Job Form
- Required fields: title, description, salary range, location, requirements
- Optional: remote options, benefits, company branding
- Validation: complete fields, reasonable salary ranges

### Compliance Review
1. **AI Pre-screen**: Flag suspicious listings
2. **Rule-based Check**: Blocked terms, prohibited categories
3. **Manual Review**: Queue for moderator approval
4. **Feedback**: Reject with specific reasons + corrections needed
5. **Appeal**: Company can contest rejection

### Job Status
- Draft - In progress
- Pending Review - Awaiting compliance check
- Approved - Live and visible
- Rejected - Non-compliant
- Suspended - Temporarily disabled

## Content Moderation

### Content Types
- Posts (company updates, news)
- Media assets (images, videos, documents)
- Pitch pages (founder presentations)
- User-generated comments

### Moderation Workflow
1. Upload submission
2. AI classification (safe/flagged)
3. Watermarking for sensitive content
4. Tiered release (private → public)
5. Report and takedown process

### Content Safety
- Automatic watermarking
- Audit trail for all content
- Age/content rating system
- Regional compliance options

## Regional Compliance

### China
- ICP license for hosting
- Content filtering requirements
- Data localization
- Real-name verification

### International
- GDPR/CCPA compliance
- Data portability
- Right to be forgotten
- Data export restrictions

## Data Models

### Verification
- `CompanyVerification` - Verification records
- `VerificationDocument` - Uploaded docs

### Compliance
- `JobCompliance` - Job review records
- `ContentModeration` - Content moderation records

## Common Tasks

### Verify Company
1. Collect documents via upload API
2. Parse and validate documents
3. Store in `CompanyVerification`
4. Add to review queue
5. Update company verification level

### Review Job Posting
1. Fetch job from `JobCompliance` queue
2. Check against compliance rules
3. Approve/reject with reasons
4. Update job status
5. Notify company of result

### Moderate Content
1. Receive content via upload handler
2. Run AI classification
3. Apply watermark if needed
4. Set visibility tier
5. Handle user reports

### Handle Appeal
1. Create appeal record
2. Re-review original decision
3. Update verification/compliance status
4. Notify user of outcome

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuhao00) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
