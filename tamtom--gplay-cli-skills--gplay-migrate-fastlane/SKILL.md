---
name: gplay-migrate-fastlane
description: Migration from Fastlane supply to gplay CLI using the gplay migrate fastlane command. Use when asked to convert a Fastlane-based Play Store workflow to gplay, or to import existing Fastlane metadata directories. Use when this capability is needed.
metadata:
  author: tamtom
---

# Fastlane Migration

Use this skill when you need to migrate from Fastlane supply to the gplay CLI.

## Preconditions
- Existing Fastlane metadata directory structure.
- gplay CLI installed and authenticated.
- Familiarity with the source Fastlane directory layout.

## Migrate Command

### Basic migration
```bash
gplay migrate fastlane \
  --source ./fastlane/metadata/android \
  --output-dir ./metadata
```

### Dry run (preview without writing files)
```bash
gplay migrate fastlane \
  --source ./fastlane/metadata/android \
  --output-dir ./metadata \
  --dry-run
```

### Migrate specific locales only
```bash
gplay migrate fastlane \
  --source ./fastlane/metadata/android \
  --output-dir ./metadata \
  --locales en-US,es-ES,fr-FR
```

### Dry run with specific locales
```bash
gplay migrate fastlane \
  --source ./fastlane/metadata/android \
  --output-dir ./metadata \
  --locales en-US,ja-JP \
  --dry-run
```

## Flags

| Flag | Description | Required |
|------|-------------|----------|
| `--source` | Path to Fastlane metadata directory | Yes |
| `--output-dir` | Output directory for gplay metadata | Yes |
| `--dry-run` | Preview changes without writing files | No |
| `--locales` | Comma-separated list of locales to migrate | No (all by default) |

## What Gets Migrated

The command converts the Fastlane directory structure into the gplay metadata format:

### Fastlane source structure
```
fastlane/metadata/android/
├── en-US/
│   ├── title.txt
│   ├── short_description.txt
│   ├── full_description.txt
│   ├── video.txt
│   ├── changelogs/
│   │   ├── 100.txt
│   │   └── 101.txt
│   └── images/
│       ├── phoneScreenshots/
│       │   ├── 1.png
│       │   └── 2.png
│       ├── icon.png
│       └── featureGraphic.png
├── es-ES/
│   └── ...
```

### Migrated output structure
```
metadata/
├── en-US/
│   ├── listing.json
│   ├── changelogs/
│   │   ├── 100.txt
│   │   └── 101.txt
│   └── images/
│       ├── phoneScreenshots/
│       │   ├── 1.png
│       │   └── 2.png
│       ├── icon.png
│       └── featureGraphic.png
├── es-ES/
│   └── ...
```

### File conversions
- `title.txt`, `short_description.txt`, `full_description.txt`, `video.txt` are consolidated into `listing.json`.
- `changelogs/` are copied as-is.
- `images/` are copied as-is.

## Workflow Examples

### Full migration from Fastlane
```bash
# 1. Preview the migration
gplay migrate fastlane \
  --source ./fastlane/metadata/android \
  --output-dir ./metadata \
  --dry-run

# 2. Run the actual migration
gplay migrate fastlane \
  --source ./fastlane/metadata/android \
  --output-dir ./metadata

# 3. Verify the output
ls -R ./metadata

# 4. Import to Play Store
gplay sync import-listings \
  --package com.example.app \
  --dir ./metadata
```

### Incremental locale migration
```bash
# Migrate English first
gplay migrate fastlane \
  --source ./fastlane/metadata/android \
  --output-dir ./metadata \
  --locales en-US

# Verify and test
gplay sync diff-listings \
  --package com.example.app \
  --dir ./metadata

# Migrate remaining locales
gplay migrate fastlane \
  --source ./fastlane/metadata/android \
  --output-dir ./metadata \
  --locales es-ES,fr-FR,de-DE,ja-JP
```

### CI/CD migration validation
```bash
# In CI, validate that migration produces expected output
gplay migrate fastlane \
  --source ./fastlane/metadata/android \
  --output-dir ./metadata-test \
  --dry-run

# Check exit code
if [ $? -eq 0 ]; then
  echo "Migration validation passed"
else
  echo "Migration validation failed"
  exit 1
fi
```

## Replacing Fastlane Supply Commands

| Fastlane Command | gplay Equivalent |
|------------------|------------------|
| `fastlane supply init` | `gplay sync export-listings --dir ./metadata` |
| `fastlane supply` | `gplay sync import-listings --dir ./metadata` |
| `fastlane supply --skip_upload_images` | `gplay sync import-listings --dir ./metadata` |
| `fastlane supply --track beta` | `gplay release --track beta --bundle app.aab` |
| `fastlane supply --track production --rollout 0.1` | `gplay release --track production --bundle app.aab --rollout 10` |

## Best Practices

1. **Always dry-run first** - Preview the migration output before writing files.
2. **Migrate locale by locale** - Start with your primary locale and verify.
3. **Keep Fastlane source** - Do not delete the Fastlane directory until fully migrated and verified.
4. **Validate after migration** - Use `gplay validate listing` to check character limits.
5. **Update CI/CD gradually** - Replace Fastlane commands one at a time.
6. **Version control the output** - Commit migrated metadata to track changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tamtom) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
