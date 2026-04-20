---
name: legal-compliance
description: > Use when this capability is needed.
metadata:
  author: luisdavidtf
---

## 1. Compliance Checklist

Before finalizing a feature or merging to `main`, run this audit:

### A. Data Protection (Privacy Policy)
1. **Minimization**: Are we collecting ONLY necessary data?
2. **Consent**: Do new forms/cookies require explicit user consent?
3. **Storage**: Is sensitive data (PII) stored securely?
   - *Check*: No PII in `localStorage` without encryption?
   - *Check*: No logging of passwords or tokens in console/server logs?
4. **ARCO Rights**: Does the user have a way to Delete/Modify this new data?
   - *Example*: If adding `pantryItems`, can the user delete them? (Yes/No)

### B. Terms & Conditions (Liability)
1. **AI Disclaimers**: If adding AI features, is the "AS IS" / "Verification Required" disclaimer visible?
2. **User generated content**: If users upload images/text, is the "Rights & Responsibility" clause visible?
3. **Age Verification**: Does the new feature allow restricted access to minors without checks?

## 2. Mandatory Verification Steps

When running this skill, you must verifying the following files:

1. `context/SettingsContext.js` -> Ensure translations for disclaimers exist.
2. `lib/db.ts` or Database Schema -> Ensure no unconsented tracking fields.
3. `middleware.ts` / `headers` -> Ensure Security Headers (CSP, HSTS) are maintained.

## 3. Audit Report Format

If requested, generate a brief report:

```markdown
## ⚖️ Legal Compliance Audit
- [x] **Data Privacy**: No new PII exposed.
- [x] **Consent**: Cookie banner covers new tracking (if any).
- [x] **Disclaimers**: AI disclaimer added to "Magic Generation".
- [x] **Right to Delete**: User can delete their own recipes/pantry items.
```

## 4. Specific Clauses Reference
- **Clause 5.1 (AI)**: "Health & Allergy Warning" -> Must be on all AI recipe generations.
- **Clause 7 (Liability)**: "Software provided AS IS" -> Standard footer/settings link.
- **Privacy Section 3**: "International Transfer" -> Vercel/Neon/Koyeb storage verification.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luisdavidtf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
