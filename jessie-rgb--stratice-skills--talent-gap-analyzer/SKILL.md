---
name: talent-gap-analyzer
description: This skill condenses a job description into technical gaps and market reality. Use when this capability is needed.
metadata:
  author: jessie-rgb
---

# Stratice Talent Gap Analyzer

## Goal
Convert a standard Job Description into a strategic "Search Blueprint" that identifies the real "needle in a haystack" requirements.

## Instructions
1. Review the `job_description` and `tech_stack`.
2. Extract the "Must-Haves" vs. "Nice-to-Haves."
3. Identify potential "Gaps" (e.g., if the salary doesn't match the seniority, or if the tech combo is rare).
4. Provide a Market Reality Check: Is this a 10-day fill or a 60-day search?
5. Output a **Delivery Plan** for the Stratice team:
   - Target companies to headhunt from.
   - Screen questions to ask candidates.

## Inputs
- `job_description`: Full text of the job req.
- `tech_stack`: Primary and secondary technologies.
- `team_context`: Current team size/structure.
- `constraints`: Budget, location, or timeline limits.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jessie-rgb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
