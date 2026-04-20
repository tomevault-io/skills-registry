---
name: appstore-info-generator
description: > Use when this capability is needed.
metadata:
  author: tddworks
---

# App Store Info Generator

Generate professional App Store listing documentation with multilingual support (EN, zh-Hans, zh-Hant).

## Workflow

1. Gather app information from user
2. Generate English content
3. Create Chinese translations
4. Validate character limits
5. Output markdown file

## Required Information

Before generating, gather from user:
- **App Name**: The app's name
- **App Purpose**: What the app does (1-2 sentences)
- **Key Features**: 3-6 main features or selling points
- **Target Audience**: Who the app is for
- **Languages**: Which locales (default: EN, zh-Hans, zh-Hant)

## Output Format

Generate markdown with this structure:

```markdown
# App Store Information

## English

**Title** (30 chars max)
```
[App Name]
```

**Subtitle** (30 chars max)
```
[Short tagline]
```

**Description**
```
[Opening hook - 1-2 sentences]

■ [Feature Category 1]

• [Feature point]
• [Feature point]

■ [Feature Category 2]

• [Feature point]
• [Feature point]

[Closing call-to-action]
```

**Keywords** (100 chars max)
```
[comma,separated,keywords]
```

---

## 简体中文 (Simplified Chinese)

[Same structure, professionally translated]

---

## 繁體中文 (Traditional Chinese)

[Same structure, professionally translated]
```

## Character Limits

| Field | Limit |
|-------|-------|
| Title | 30 chars |
| Subtitle | 30 chars |
| Keywords | 100 chars |

## Writing Guidelines

### Title
- Clear, memorable name
- Avoid generic words unless part of brand

### Subtitle
- Complement title, don't repeat it
- Describe primary function

### Description
- Hook in first 1-2 lines (visible in search)
- Use ■ for section headers
- Use • for bullet points
- 3-5 feature categories
- End with call-to-action

### Keywords
- Comma-separated, no spaces after commas
- Include synonyms and related terms
- Don't repeat words from title

### Chinese Localization
- 简体中文: Mainland China conventions
- 繁體中文: Taiwan conventions
- Translate meaning, not word-for-word

## Optional Sections

### Promotional Text
For special offers or launch promotions.

### Release Notes
Format:
```markdown
### v[X.X.X]

**English**
```
■ [Headline]

• [Change 1]
• [Change 2]
```

**简体中文**
```
[Translation]
```

**繁體中文**
```
[Translation]
```
```

## Output Location

Save to: `docs/appstore-info.md` or user-specified path.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tddworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
