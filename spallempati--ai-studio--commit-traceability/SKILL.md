---
name: commit-traceability
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Enforce Traceability Between Commits, Work Items, and Acceptance Tests

## Description

This rule mandates that every commit must reference at least one tracked work
item—such as a Jira ticket or bug ID—and must be verifiably linked to automated
tests that demonstrate acceptance. It ensures end-to-end traceability from
business requirements through development and verification.

## Purpose

To strengthen auditability, defect tracking, and quality assurance by linking
code changes to specific requirements and confirming those requirements through
passing automated tests. This practice supports secure SDLC compliance and agile
traceability mandates.

## Scope

- All protected branches and tracked commits
- Feature branches and hotfixes
- Java, Python, TypeScript, and feature test files
- Applies to all developers and QA contributors

## SDLC Integration

- **Planning**: Work items have unique ticket IDs and test criteria
- **Analysis**: Acceptance criteria mapped to test tags
- **Design**: Coverage of scenarios ensured by traceable tests
- **Development**: Commits and PRs must include ticket IDs
- **Testing**: Tests must be tagged and matched to tickets
- **Deployment**: Only verified, ticket-linked code is deployable
- **Maintenance**: Full history of changes and why they were made

## Standards

### Traceability and Verification

- Every commit **MUST** include a valid reference to a story, task, or defect
  (e.g., `ABC-1234`)
- Associated tests **MUST** contain a matching tag or annotation for the
  referenced ticket
- Pull requests **MUST** be rejected if no ticket reference is detected
- CI pipelines **MUST** validate test-tag presence for referenced tickets

## Actionable Metrics

| Metric                          | Target Value      | Measurement Method            | Enforcement Level |
| ------------------------------- | ----------------- | ----------------------------- | ----------------- |
| Commits linked to work items    | ≥ 90 %            | Git log + regex pattern match | **MUST**          |
| Tests with matching ticket tags | 100 % (if ticket) | Test annotation scanner       | **MUST**          |
| PRs with ticket ID in title     | 100 %             | Regex on PR metadata          | **MUST**          |

## Implementation

### Configuration Requirements

- Use conventional commit formats or PR naming with ticket ID prefix (e.g.,
  `ABC-1234`)
- Annotate automated tests with ticket tags (`@ABC-1234`)
- Configure CI to fail PRs missing linked ticket or tag mismatch

#### Example: Correct Implementation

```bash
# Git commit or PR title
git commit -m "ABC-1234 Add bulk upload support"

# Acceptance test tag
@Test
@Tag("ABC-1234")
void shouldSupportBulkUpload() {
    ...
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
