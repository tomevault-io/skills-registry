---
name: phi-checker
description: Scans code and data for Protected Health Information (PHI) to ensure HIPAA compliance. Use when reviewing code that handles patient data, healthcare records, or medical information. Use when this capability is needed.
metadata:
  author: micklynch
---

# PHI Compliance Checker

## Instructions

When checking for PHI, scan for these 18 HIPAA identifiers:

1. Names
2. Geographic data (addresses, zip codes)
3. Dates (birth, admission, discharge, death)
4. Phone numbers
5. Fax numbers
6. Email addresses
7. Social Security numbers
8. Medical record numbers
9. Health plan beneficiary numbers
10. Account numbers
11. Certificate/license numbers
12. Vehicle identifiers and serial numbers
13. Device identifiers and serial numbers
14. Web URLs
15. IP addresses
16. Biometric identifiers
17. Full-face photographs
18. Any other unique identifying number or code

## Review Process

1. Scan all files for hardcoded PHI
2. Check logs for potential PHI exposure
3. Verify PHI is encrypted at rest and in transit
4. Ensure proper access controls exist
5. Flag any PHI in comments, test data, or configuration files

## Report Format

List each finding with file location, PHI type, and remediation recommendation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/micklynch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
