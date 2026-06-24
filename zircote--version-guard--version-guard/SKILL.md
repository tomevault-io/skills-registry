---
name: version-guard
description: > Use when this capability is needed.
metadata:
  author: zircote
---

# Version Guard — Proactive Version Verification

You are a version-aware assistant. Before recommending any library version, you MUST verify
the current stable release against live documentation. Never trust training-data version knowledge.

## When to Activate

Trigger automatically when you detect any of these contexts:

- About to recommend a specific package version
- Writing an install command (`npm install`, `pip install`, `cargo add`, `go get`, etc.)
- Adding or modifying dependency files
- Writing code that imports version-sensitive APIs
- User asks about versions, compatibility, or "latest"
- Generating project scaffolds or starter templates

## Verification Workflow

### Step 1 — Load Context7 Tools

The context7 MCP tools are deferred. Load them first:

```
ToolSearch query: "+context7"
```

This loads `mcp__context7__resolve-library-id` and `mcp__context7__query-docs`.

### Step 2 — Resolve the Library

```
mcp__context7__resolve-library-id
  libraryName: "<package-name>"
  query: "latest version installation"
```

Select the result with the highest relevance and documentation coverage.

### Step 3 — Query Current Documentation

```
mcp__context7__query-docs
  libraryId: "<resolved-id>"
  query: "latest version installation getting started"
```

Extract:
- Current stable version number
- Installation instructions
- Breaking changes from prior major versions

### Step 4 — Apply the Verified Version

- Use the verified latest stable version in all recommendations and install commands
- If the verified version differs from training data, note explicitly:
  `"Using <library> v<latest> (verified current — training data may reference v<old>)"`

### Step 5 — Flag Breaking Changes

When upgrading from an older version:
- Note major API changes briefly
- Reference migration docs if available
- Warn about deprecated patterns

## Fallback Strategy

If context7 cannot resolve the library:
1. Search the web for `"<library> latest version <current-year>"`
2. Check GitHub releases or the package registry (npm, PyPI, crates.io, etc.)
3. State the source: `"Per <source>, latest stable is v<X>"`

## Ecosystem Reference

For dependency file formats and context7 naming conventions across all major ecosystems,
see [../../references/ecosystem-patterns.md](../../references/ecosystem-patterns.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zircote) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
