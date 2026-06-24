---
name: dochia-explain
description: > Use when this capability is needed.
metadata:
  author: dochia-dev
---

## Overview

`dochia explain` provides detailed information about playbooks, mutators, response codes, and error reasons. This is
useful for understanding test results and debugging failures.

## Basic Usage

```bash
# Explain a 9XX response code
dochia explain --type response_code 953

# Explain a playbook
dochia explain --type playbook BypassAuthentication

# Explain a mutator
dochia explain --type mutator RandomString

# Explain an error reason
dochia explain --type error_reason "Error details leak"
```

## Options

| Option       | Description                                                                |
|--------------|----------------------------------------------------------------------------|
| `-t, --type` | Type to explain: PLAYBOOK, MUTATOR, RESPONSE_CODE, ERROR_REASON (required) |
| `<info>`     | The name, code, or search term to explain (positional argument)            |

## Types

- **PLAYBOOK** — Search and describe test playbooks by name (partial match supported)
- **MUTATOR** — Search and describe mutators by name (partial match supported)
- **RESPONSE_CODE** — Explain Dochia's custom 9XX response codes and standard HTTP codes
- **ERROR_REASON** — Explain error reasons found in test reports (partial match supported)

## Examples

```bash
# Find all playbooks related to "json"
dochia explain --type playbook json

# Understand what response code 999 means
dochia explain --type response_code 999

# Search for error reasons containing "leak"
dochia explain --type error_reason leak
```

## Documentation

Full reference: https://docs.dochia.dev/cli/explain

---
> Source: [dochia-dev/dochia-cli](https://github.com/dochia-dev/dochia-cli) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
