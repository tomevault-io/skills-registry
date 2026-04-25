---
name: release-tagging-plan
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Release Composition & Tagging Plan

## Intent

Plan immutable releases with tags that reliably identify the source state used
to build and deploy each component version.

---

## When to Use

- Before creating a release for any component
- Before deploying to environments where traceability is required
- When reviewing release automation conventions

---

## Precondition Failure Signal

- Releases cannot be traced to a specific source state
- Tags are missing, inconsistent, or mutable
- Multiple different sources produce the “same” release version
- Environment deployments are not linked to component tags/versions

---

## Postcondition Success Signal

- Each release version maps to a specific tag and source state
- Tags are immutable and consistent across components
- Release composition is explicit for multi-component changes
- Deployment inputs reference tags/versions, not floating branches

---

## Process

1. **Source Review**: Inspect the impact set and the current versions of all impacted components.
2. **Implementation**: Determine the correct semantic version bump for each component and define the new tags following repository conventions.
3. **Verification**: Verify that the proposed tags are unique and correctly map to the current source code state (commit hash).
4. **Documentation**: Record the release tagging plan (component, version, tag, commit hash).
5. **Review**: Release Manager/SRE and Platform Engineer review the tagging plan for accuracy and provenance.

---

## Example Test / Validation

- Given a release version, verify a unique tag points to the source used
- Verify deployment records can map environments to component versions/tags
- Confirm that re-running a build from the tag yields the same source inputs

---

## Common Red Flags / Guardrail Violations

- Deploying from main without a tag
- Re-tagging or moving tags after release
- “Latest” deployments that lack explicit version references
- Bundling multiple components into one tag without a clear scheme

---

## Recommended Review Personas

- **Release/Platform Engineer** – validates traceability and immutability
- **Tech Lead** – validates release scope and component mapping
- **Security Reviewer** – validates integrity of release provenance (where applicable)

---

## Skill Priority

P0 – Safety & Integrity

---

## Conflict Resolution Rules

- Overrides all lower-priority skills
- No deployment without tag-based traceability unless explicitly escalated
- If tag scheme conflicts exist, default to the most traceable and component-scoped approach

---

## Conceptual Dependencies

- incremental-change-impact
- semantic-version-impact

---

## Classification

Core  
Governance

---

## Notes

If you cannot answer “what code built this release?” the release process is broken.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
