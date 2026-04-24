---
name: web-design-guidelines
description: Review UI code for Web Interface Guidelines compliance. Use when reviewing UI code, checking accessibility standards, auditing design consistency, or validating sites against best practices. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# Web Interface Guidelines

Review files for compliance with Web Interface Guidelines.

## How It Works

1. Fetch the latest guidelines from the source URL below
2. Read the specified files (or prompt user for files/pattern)
3. Check against all rules in the fetched guidelines
4. Output findings in the terse `file:line` format

## Guidelines Source

Fetch fresh guidelines before each review:

```
https://raw.githubusercontent.com/vercel-labs/web-interface-guidelines/main/command.md
```

Use WebFetch to retrieve the latest rules. The fetched content contains all the rules and output format instructions.

## Usage

When a user provides a file or pattern argument:

1. Fetch guidelines from the source URL above
2. Read the specified files
3. Apply all rules from the fetched guidelines
4. Output findings using the format specified in the guidelines

If no files specified, ask the user which files to review.

## Outputs & Deliverables

- **Primary Output**: Accessibility and design review findings in `file:line` format and a short remediation list
- **Secondary Output**: Suggested code snippets or CSS fixes for small violations
- **Success Criteria**: No critical accessibility failures and clear remediation steps for others
- **Quality Gate**: `implementer` review for fixes prior to merge

## Constraints

- **Technical Constraints:** Do not modify source files automatically; produce recommended fixes only
- **Scope Constraints:** Focus on UI guidelines and accessibility; visual design approvals are separate
- **Governance Constraints:** Use latest guidelines from the canonical source before each review

## Common Pitfalls

- **Stale Guidelines**: Using local cached guidelines instead of fetching fresh ones. Always fetch from the canonical source before each review.
- **Skipping Accessibility**: Checking only visual design, not keyboard navigation or screen reader support. Accessibility is mandatory, not optional.
- **Ignoring Contrast Ratios**: Visual text looks fine but fails WCAG contrast requirements. Always validate color contrasts programmatically.
- **Missing Responsive Testing**: UI looks good on desktop but breaks on mobile. Test at multiple breakpoints.
- **Not Suggesting Fixes**: Reporting problems without showing how to fix them. Always provide remediation code snippets.
- **Vague Findings**: "Design looks off" instead of specific violations. Reference exact lines and rules.

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Review Request | UI code files | Guidelines fetch | Retrieve canonical guidelines from source |
| Analysis | Code patterns and styles | Findings report | Identify violations against guidelines |
| Remediation | Violations found | `implementer` | Provide code examples for fixes |
| Merge Gate | UI changes complete | QA approval | No critical accessibility issues before merge |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
