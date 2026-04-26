---
name: doc-adr-architect
description: Documenting significant architectural decisions to preserve project context and evolution. Use when this capability is needed.
metadata:
  author: jcorpac
---

# ADR Architect

Architecture Decision Records (ADRs) capture the *why* behind significant technical choices.

## Why ADRs?
- **Avoid "Second-Guessing"**: Document why an alternative was rejected.
- **Onboard Newcomers**: Provide historical context for existing designs.
- **Team Alignment**: Ensure everyone understands the rationale for a decision.

## Standard ADR Format

### 1. Title
State the decision clearly (e.g., "ADR-001: Use PostgreSQL for Persistent Storage").

### 2. Status
Proposed, Accepted, Superceded, or Rejected.

### 3. Context
What is the problem? What are the constraints? What alternatives were considered?

### 4. Decision
What choice are we making? Be specific.

### 5. Consequences
What are the positive and negative side effects? What trade-offs were made? (e.g., "Slower writes, but better consistency").

## Best Practices
- **Write them as you decide**: Don't try to reconstruct them months later.
- **Link them**: Show when one ADR supercedes another.
- **Keep them concise**: Focus on the rationale, not the entire meeting transcript.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
