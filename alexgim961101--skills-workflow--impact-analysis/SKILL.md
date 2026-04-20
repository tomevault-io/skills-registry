---
name: impact-analysis
description: | Use when this capability is needed.
metadata:
  author: alexgim961101
---

# Impact Analysis

## Goal
Identify the blast radius and potential risks before making changes.
Analyze to the level where "how far does this affect?" can be answered quantitatively.

## Instructions

### Step 1: Identify Change Targets

List the files/resources that directly require modification from the change request.

- **Code changes**: Scan related modules, services, endpoints, DB schemas
- **Infrastructure changes**: Identify target resources (K8s, DB, network, storage, etc.)
- **Output**: List of files/resources to be changed

### Step 2: Dependency Tracing (Blast Radius)

Trace the scope that may be affected but is not a direct change target.

**Code changes:**
- [ ] What other modules import/call this module?
- [ ] Where are the changed interfaces/DTOs used?
- [ ] What are the related test files?
- [ ] Are configuration files (config, env) affected?

**Infrastructure changes:**
- [ ] What services/applications depend on this resource?
- [ ] Are network paths (DNS, LB, firewall) affected?
- [ ] Does this propagate to other environments (dev/stage/prod)?

### Step 3: Breaking Change Assessment

Determine whether changes are breaking based on the criteria below.

| Change Type | Breaking? | Example |
|-------------|-----------|---------|
| API signature change | ⚠️ Possible | Parameter add/remove, response structure change |
| DB schema change | ⚠️ Possible | Column drop, NOT NULL added, type change |
| Config value change | ⚠️ Possible | Env variable rename, default value change |
| Internal refactoring | ✅ Safe | Function split, private rename, logic improvement |
| New file/endpoint addition | ✅ Safe | Addition only, no existing code modification |

### Step 4: Risk Summary

Summarize findings in the format below.

```
📋 Impact Analysis Report

Change targets: N files/resources
Blast radius: M files/services (including dependencies)

⚠️ Breaking Changes:
  - [Location] Change description → Affected areas

🔍 Caution:
  - [Risk level: High/Med/Low] Description

✅ Safe Areas:
  - Summary of unaffected areas
```

## Constraints
- When change target files can be directly accessed, always read and analyze the code
- Do not make assumptions like "it's probably fine" — state uncertainties explicitly
- Always notify the user when breaking changes are found

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexgim961101) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
