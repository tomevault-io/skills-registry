---
name: cyclonedx-spec
description: > Use when this capability is needed.
metadata:
  author: CycloneDX
---

# CycloneDX Specification Skill

Authoritative guidance for the CycloneDX Bill of Materials standard. CycloneDX is an Ecma International standard published as ECMA-424 under a royalty-free patent policy. Version 1.6 is the 1st Edition (June 2024) and version 1.7 is the 2nd Edition (December 2025). The JSON Schema is the reference implementation.

## Scope

- **Versions:** 1.6 (ECMA-424 1st Edition) and 1.7 (ECMA-424 2nd Edition)
- **Formats:** JSON and XML; Protocol Buffers also registered
- **Schema dialect:** JSON Schema draft-07

## Lookup Order

For any CycloneDX request, consult references in this order:

1. **Use case examples** in `references/use-cases/`. Start here for any scenario; these contain worked JSON and XML. See `references/use-cases/INDEX.md` to pick the right file.
2. **Capability overviews** in `references/capabilities/` for short BOM-type summaries. See `references/capabilities/INDEX.md`.
3. **Authoritative guides** in `references/guides/<BOM-TYPE>/en/*.md` for best practices and detailed prose. See `references/guides/INDEX.md`. Only SBOM, CBOM, ML-BOM, MBOM, and Attestations have full guides.
4. **Schemas** in `references/schemas/bom-1.6.schema.json` or `bom-1.7.schema.json` to verify field names, types, enumerations, and constraints. Focus on the relevant `definitions` section; do not read whole schemas.
5. **Authoring conventions** in `references/iso-house-style.md`, `oxford-english.md`, and `json-schema-patterns.md` when writing or editing specification prose.

## Reference Map

| Path | Purpose |
|---|---|
| `references/schemas/` | JSON schemas (bom-1.6, bom-1.7, spdx, jsf-0.82, cryptography-defs) |
| `references/use-cases/` | 40+ scenario files with JSON/XML examples (see `INDEX.md`) |
| `references/capabilities/` | 13 BOM-type overview files (see `INDEX.md`) |
| `references/guides/` | Five full authoritative guides in Markdown (see `INDEX.md`) |
| `references/about/` | Project governance, history, TC54, standardization process |
| `references/iso-house-style.md` | ISO House Style for specification prose |
| `references/oxford-english.md` | Oxford English spelling rules for spec prose |
| `references/json-schema-patterns.md` | draft-07 patterns used in CycloneDX schemas |
| `references/property-taxonomy.md` | `cdx:` namespace registry and extensibility guidance |
| `references/conventions.md` | Required BOM fields, minimal examples, media types, validation |
| `references/version-diff.md` | What's new in 1.7 vs 1.6; version selection guidance |

## How to Work

### Specification Authoring (highest fidelity bar)

When writing or editing CycloneDX specification text, read `references/iso-house-style.md` and `references/oxford-english.md` first. Core rules:

- Use ISO verbal forms (`shall`, `should`, `may`, `can`) in lowercase. Do not use RFC 2119 keywords such as MUST, REQUIRED, or OPTIONAL. Reserve `shall` for true conformance requirements; use `can` for capabilities.
- Write in present tense, active voice, impersonal third person (no "you", "we", or "I"). No contractions.
- Use Oxford English spelling for spec prose (e.g., `standardize` with `-ize`, `behaviour`, `sulfur`, `artefact`, `licence` as noun and `license` as verb). Exception: when referring to a specification-defined term or schema property, use the spelling used in the schema itself (for example, the `license` property and the `artifact` enum values keep their American spellings in prose).
- Every object property in the JSON Schema includes `type`, `title`, and `description` at minimum.
- All object definitions use `additionalProperties: false`.
- Enumeration values include `meta:enum` objects with human-readable descriptions.
- A preprocessing script normalises schema description fields to ISO House Style, so authored text should already conform.

### BOM Generation

Load `references/conventions.md` for required top-level fields, a minimal valid JSON BOM, a minimal valid XML BOM, media types, and file naming. For real-world patterns, load the matching `references/use-cases/*.md`. Always read the target version schema before producing output.

### Concept Explanation

Ground answers in actual schema definitions. Key concepts:

- **BOM types.** SBOM, HBOM, OBOM, SaaSBOM, AI/ML-BOM, CBOM, MBOM, VDR, VEX, BOV all share one unified schema.
- **Component types (1.7).** `application`, `framework`, `library`, `container`, `platform`, `operating-system`, `device`, `device-driver`, `firmware`, `file`, `machine-learning-model`, `data`, `cryptographic-asset`.
- **Identity.** Components identify via `cpe`, `purl`, `omniborId`, `swhid`, or `swid`. Evidence of identity is modeled under `evidence.identity`.
- **Dependencies.** The `dependencies` array models a directed graph with `ref` (the component) and `dependsOn` (its direct dependencies). The `provides` field documents which specifications a component implements.
- **Compositions.** Precisely describe completeness using `aggregate` values like `complete`, `incomplete`, `unknown`, or first/third-party scoped variants.
- **Vulnerabilities.** Each vulnerability supports `ratings` (CVSS, OWASP, SSVC), `analysis` (state, justification, response), and `affects` (components and version ranges).
- **Declarations and Attestations (1.6+).** Assessors, attestations, claims, evidence, and signatories enable compliance as code.
- **Formulation (1.6+, scope extended in 1.7).** Describes how components and services were manufactured or deployed via formulas, workflows, tasks, and steps.
- **Cryptographic Properties (1.6+, enhanced in 1.7).** Model algorithms, certificates, protocols, and related material. 1.7 adds the Cryptography Registry via `cryptography-defs.schema.json`.
- **Citations (1.7).** Attribute specific BOM data to contributing entities using JSON Pointers or path expressions.
- **Patents (1.7).** Model patent information, families, and assertions on components or services.

For 1.6 vs 1.7 detail, load `references/version-diff.md`.

### Schema Development

- CycloneDX uses JSON Schema **draft-07**.
- Read `references/json-schema-patterns.md` for recurring patterns.
- Cross-references between objects use `bom-ref` (identifier) and `refLinkType` (pointer to an identifier).
- External schema references use `$ref` to subschema files (`spdx.schema.json`, `jsf-0.82.schema.json`, `cryptography-defs.schema.json`).
- BOM-Link crosses between BOMs using URN format: `urn:cdx:<uuid>/<version>#<bom-ref>`.

## Notes

- Both versions are ratified Ecma International standards published as ECMA-424. CycloneDX 1.6 is the 1st Edition (June 2024). CycloneDX 1.7 is the 2nd Edition (December 2025) and adds citations, patents, distribution constraints (TLP), external component markers, license-choice restructuring, and the cryptography registry. Use 1.7 as the default for new work.
- Schemas are large (each roughly 200 KB). Always focus on the relevant `definitions` entry rather than loading an entire schema.
- Property taxonomy is split between `cdx:` (standardized) and arbitrary namespaces. The `cdx` namespace is authoritative; see `references/property-taxonomy.md`.

---
> Source: [CycloneDX/skills](https://github.com/CycloneDX/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
