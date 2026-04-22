---
name: 4d-create-project
description: Create a new 4D project from scratch. Use this skill when the user wants to initialize, create, or start a new 4D project. Creates the required folder structure and .4DProject configuration file. Use when this capability is needed.
metadata:
  author: e-marchand
---

# 4D Project Creator

## Required Structure

```
<ProjectName>/
├── Project/
│   ├── Sources/
│   │   ├── Methods/
│   │   └── Classes/
│   └── <ProjectName>.4DProject
```

## .4DProject File Content

```json
{
    "compatibilityVersion": 2100,
    "tokenizedText": false
}
```

- `compatibilityVersion`: 2100 for 4D v21+
- `tokenizedText`: false for readable source code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/e-marchand) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
