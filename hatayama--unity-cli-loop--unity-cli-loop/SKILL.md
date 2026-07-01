---
name: uloop-get-project-info
description: Get Unity project information via uloop CLI. Use when you need to: (1) Check Unity Editor version, (2) Get project settings and platform info, (3) Retrieve project metadata for diagnostics. Use when this capability is needed.
metadata:
  author: hatayama
---

# uloop get-project-info

Get detailed Unity project information.

## Usage

```bash
uloop get-project-info
```

## Parameters

None.

## Examples

```bash
# Get project info
uloop get-project-info
```

## Output

Returns JSON with project information:
- Unity version
- Project name
- Platform settings
- Build target
- Other project metadata

---
> Source: [hatayama/unity-cli-loop](https://github.com/hatayama/unity-cli-loop) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-01 -->
