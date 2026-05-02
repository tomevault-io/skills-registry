---
name: resume-writing
description: Resume parsing, matching, customization, and export workflows for ATS-friendly resumes. Use when asked to parse resumes, compare to job postings, generate tailored resume variants, or preserve formatting in PDF/DOCX. Use when this capability is needed.
metadata:
  author: zeyadhq
---

# Resume Writing

## Quick Start
- Determine input type (resume file, resume text, job posting text/URL).
- Parse resume and job requirements, then run matching and gap analysis.
- Generate a customized resume variant that preserves formatting and truthfulness.

## Inputs
- Resume file (PDF/DOCX) or structured resume data.
- Job description text or URL.
- Optional: preferred format (PDF/DOCX), target role, constraints (length, emphasis).

## Outputs
- Structured resume data (sections, fields, metadata).
- Match report (gaps, strengths, match score).
- Customized resume variant (PDF/DOCX) with audit metadata.

## Workflow
1. Validate file type and size before parsing.
2. Parse resume into structured data and normalize dates/skills.
3. Parse job description into requirements and classifications.
4. Match resume to job requirements and compute gaps/strengths.
5. Generate customized resume variant by reordering and context-safe keyword insertion.
6. Preserve original formatting and export to the requested format.
7. Store variant metadata and maintain audit trail.

## Truthfulness Guardrails
- Add or move only content already present in resume evidence.
- Reject any suggestion that invents experience, credentials, or outcomes.

## Quality Gates
- Parsing accuracy >= 95% on labeled fixtures.
- Customization latency P95 < 3s.
- PDF/DOCX export preserves formatting.

## Use References and Templates
- For commands and hooks, read `references/commands.md` and `references/hooks.md`.
- For resume templates, use files in `assets/templates/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zeyadhq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
