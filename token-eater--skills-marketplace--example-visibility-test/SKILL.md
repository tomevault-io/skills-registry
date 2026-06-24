---
name: example-visibility-test
description: Tests marketplace visibility configurations and catalog tiers (preview catalog only)
version: 1.0.0
author: Skills Marketplace Team
category: examples
tags:
  - testing
  - preview
  - development
---

# Example Visibility Test

This skill demonstrates the **two-tier catalog system** and is used for testing marketplace visibility configurations.

## Purpose

- 🧪 Test preview catalog functionality
- 📊 Verify catalog tier system
- 🔍 Validate skill visibility

This skill intentionally appears **only in the preview catalog** to demonstrate the two-tier system.

## Catalog Tiers

The Skills Marketplace uses a two-tier catalog system:

### Stable Catalog (`marketplace.json`)

**Purpose:** Production-ready skills

- ✅ Fully tested and documented
- ✅ Stable, reliable, production-ready
- ✅ Semantic versioning guaranteed
- ✅ Suitable for general use

### Preview Catalog (`marketplace-preview.json`)

**Purpose:** Beta skills and early access

- 🧪 Experimental features
- 🚀 Early access to new skills
- ⚠️ May have breaking changes
- 🧑‍💻 Perfect for testing and feedback

## Skill Lifecycle

Skills typically progress through these stages:

```
Development → Preview Catalog → Testing → Stable Catalog
     ↓              ↓              ↓            ↓
   Local        Beta Users    Feedback    Production
```

1. **Local Development** - Created and tested locally
2. **Preview Catalog** - Submitted for community testing
3. **Testing Period** - Community provides feedback
4. **Stable Catalog** - Promoted after successful testing

## This Skill's Visibility

**This skill appears in:**
- ✅ Preview catalog (`marketplace-preview.json`)

**This skill does NOT appear in:**
- ❌ Stable catalog (`marketplace.json`)

This demonstrates that skills can be in preview only, stable only, or both.

## Installation

### From Preview Catalog

```bash
# Add marketplace with preview catalog
/plugin marketplace add token-eater/skills-marketplace?ref=.claude-plugin/marketplace-preview.json

# Install this skill
/plugin install example-visibility-test
```

### From Stable Catalog

```bash
# Add marketplace (default: stable catalog)
/plugin marketplace add token-eater/skills-marketplace

# This skill will NOT appear (it's preview-only)
/plugin list
```

## Testing Visibility

Use this skill to test catalog configurations:

### Test 1: Stable Catalog

```bash
# Add stable catalog
/plugin marketplace add token-eater/skills-marketplace

# List skills
/plugin list

# Expected: example-visibility-test NOT in list
```

### Test 2: Preview Catalog

```bash
# Add preview catalog
/plugin marketplace add token-eater/skills-marketplace?ref=.claude-plugin/marketplace-preview.json

# List skills
/plugin list

# Expected: example-visibility-test IS in list
```

### Test 3: Local Development

```bash
# Add local marketplace
cd /path/to/skills-marketplace
/plugin marketplace add .

# List skills
/plugin list

# Expected: All skills visible (including this one)
```

## Use Cases for Preview Catalog

Skills appropriate for preview catalog:

- 🧪 **Experimental features** - Testing new functionality
- 🚀 **Early access** - Get feedback before stable release
- 🔄 **Active development** - Rapidly changing
- 📝 **Incomplete docs** - Documentation in progress
- ⚠️ **Breaking changes** - API not finalized

## Promotion to Stable

After testing in preview, skills are promoted to stable when:

1. ✅ **Testing complete** - Verified by community
2. ✅ **Feedback addressed** - Issues resolved
3. ✅ **Documentation complete** - Fully documented
4. ✅ **Stable API** - No breaking changes expected
5. ✅ **Maintainer approval** - Project maintainers approve

## For Skill Developers

If you're testing a new skill:

1. **Submit to preview catalog** - Add to `marketplace-preview.json`
2. **Request testers** - Ask community for feedback
3. **Iterate** - Fix issues, improve documentation
4. **Request promotion** - Ask maintainers to move to stable

## Resources

- 📖 [Creating Skills Guide](../../docs/creating-skills.md)
- 🤝 [Contributing Guide](../../docs/contributing.md)
- 📥 [Installation Guide](../../docs/installation.md)
- 🏗️ [Architecture Guide](../../docs/architecture.md)

## Support

- 💬 [GitHub Discussions](https://github.com/token-eater/skills-marketplace/discussions)
- 🐛 [Report Issues](https://github.com/token-eater/skills-marketplace/issues)

---

**For contributors:** Use this skill as a reference when submitting new skills to the preview catalog.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/token-eater) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
