---
name: restapi-translations
description: Управление переводами REST API ключей (rest_*) для MikoPBX. Автоматически находит отсутствующие русские ключи в RestApi.php и синхронизирует их с исходным кодом. Использовать при проверке переводов API, после добавления новых endpoints или перед релизом. Use when this capability is needed.
metadata:
  author: mikopbx
---

# REST API Translation Management

Автоматическое управление русскими переводами REST API ключей для документации OpenAPI в MikoPBX.

## What This Skill Does

1. **Extracts rest_* keys** from PBXCoreREST source code (~1589 unique keys)
2. **Validates translations** comparing code keys with RestApi.php
3. **Finds missing keys** (in code but not in RestApi.php) - 219 keys
4. **Finds unused keys** (in RestApi.php but not used) - 358 keys
5. **Synchronizes RestApi.php** adding/removing keys with backups
6. **Validates PHP syntax** after changes

## When to Use This Skill

Automatically activate when:
- User asks "check REST API translations" / "проверь переводы REST API"
- User says "sync RestApi.php" / "синхронизируй RestApi.php"
- User asks "find missing rest_* keys" / "найди отсутствующие ключи rest_*"
- After adding new REST API endpoints
- Before creating a new release

DO NOT activate when:
- User asks about general translation management (use `translations` skill)
- User needs to translate to other languages (not Russian)

## How It Works

### Three-Step Workflow

```bash
cd .claude/skills/restapi-translations/scripts

# Step 1: Extract keys from code
python3 extract_keys.py

# Step 2: Validate translations
python3 validate_translations.py

# Step 3: Sync RestApi.php
python3 sync_translations.py --add-missing
```

### Interactive Mode

```bash
./manage_translations.sh

# Menu:
# 1. Extract keys from source code
# 2. Validate translations
# 3. Add missing keys
# 4. Remove unused keys
# 5. Full sync (add + remove)
# 6. Preview changes (dry run)
# 7. Run all (extract + validate + sync)
```

## Key Patterns

### 1. API Resource Descriptions
```php
'rest_Extensions_ApiDescription' => 'Комплексное управление внутренними номерами...'
```
Pattern: `rest_{ResourceName}_ApiDescription`

### 2. Operation Summaries & Descriptions
```php
'rest_ext_GetList' => 'Получить список внутренних номеров'
'rest_ext_GetListDesc' => 'Получить пагинированный список всех...'
```
Pattern: `rest_{abbr}_{Operation}[Desc]`

### 3. HTTP Responses
```php
'rest_response_200_get' => 'Запись успешно получена'
'rest_response_404_not_found' => 'Запись не найдена'
```
Pattern: `rest_response_{code}_{type}`

### 4. Parameters, Schemas, Security
```php
'rest_param_name' => 'Название'
'rest_schema_extension_list' => 'Список внутренних номеров'
'rest_security_bearer' => 'JWT Bearer Token аутентификация'
```

## Core Principles

### ✅ DO:
- Run extraction before validation
- Use --dry-run to preview changes
- Translate placeholder text after adding keys
- Review unused keys carefully (might be for future features)

### ❌ DON'T:
- Skip extraction step (validation needs extracted_keys.json)
- Remove unused keys without reviewing them
- Edit RestApi.php manually (use sync script)
- Ignore `[ТРЕБУЕТ ПЕРЕВОДА]` placeholders

## Typical Workflow

### When Creating New Endpoint

```bash
# 1. Write controller
vim src/PBXCoreREST/Controllers/MyResource/RestController.php

# 2. Extract + validate + sync
./manage_translations.sh all

# 3. Translate placeholders
grep "ТРЕБУЕТ ПЕРЕВОДА" src/Common/Messages/ru/RestApi.php
vim src/Common/Messages/ru/RestApi.php

# 4. Verify
python3 validate_translations.py

# 5. Commit
git add src/PBXCoreREST/Controllers/MyResource/
git add src/Common/Messages/ru/RestApi.php
git commit -m "feat: add MyResource REST API endpoints"
```

### Monthly Maintenance

```bash
# Check for drift
./manage_translations.sh validate

# Review unused keys with team

# Clean up if agreed
./manage_translations.sh sync --remove-unused
```

### Before Release

```bash
# Complete check
./manage_translations.sh all

# Ensure no placeholders
grep "ТРЕБУЕТ ПЕРЕВОДА" src/Common/Messages/ru/RestApi.php

# Commit if changes
git add src/Common/Messages/ru/RestApi.php
git commit -m "chore: sync REST API translations"
```

## Placeholder Generation

Script generates context-aware Russian placeholders:

- `GetList` → "Получить список [ТРЕБУЕТ ПЕРЕВОДА]"
- `Create` → "Создать запись [ТРЕБУЕТ ПЕРЕВОДА]"
- `Update` → "Обновить запись [ТРЕБУЕТ ПЕРЕВОДА]"
- `Delete` → "Удалить запись [ТРЕБУЕТ ПЕРЕВОДА]"
- `_ApiDescription` → "Описание API ресурса [ТРЕБУЕТ ПЕРЕВОДА]"
- `rest_response_` → "Описание HTTP ответа [ТРЕБУЕТ ПЕРЕВОДА]"

**Always translate `[ТРЕБУЕТ ПЕРЕВОДА]` to proper Russian text!**

## Safety Features

1. **Automatic Backups** - `RestApi.php.bak.YYYYMMDD_HHMMSS` before changes
2. **PHP Syntax Validation** - Auto-restores if invalid
3. **Dry Run Mode** - Preview with `--dry-run`
4. **User Confirmation** - Asks before removing keys
5. **Rollback Support** - Manual restore from `.bak` files

## Output Examples

### Validation Output
```
======================================================================
VALIDATION RESULTS
======================================================================

✅ Valid keys:    1370/1589 (86%)
❌ Missing keys:  219 (in code, not in RestApi.php)
⚠️  Unused keys:   358 (in RestApi.php, not used in code)

Missing Keys:
  rest_ext_GetList (Controllers/Extensions/RestController.php:123)
  rest_fw_CreateDesc (Controllers/Firewall/RestController.php:89)
```

### Sync Output
```
======================================================================
REST API TRANSLATION SYNCHRONIZATION
======================================================================

➕ Adding 219 missing keys...
✅ Backup created: RestApi.php.bak.20251024_143022
🔍 Validating PHP syntax...
✅ PHP syntax is valid

Next steps:
  1. Review changes: git diff src/Common/Messages/ru/RestApi.php
  2. Translate placeholder text from Russian
  3. Run validate_translations.py to verify
```

## Troubleshooting

### Error: "Extracted keys file not found"
**Fix:** Run `python3 extract_keys.py` first

### Error: "PHP syntax error detected"
**Fix:** Script auto-restores from backup

### Too many unused keys
**Fix:** Review carefully, check if for future features, ask team

### Placeholders not translated
**Fix:** `grep -n "ТРЕБУЕТ ПЕРЕВОДА" src/Common/Messages/ru/RestApi.php` and edit

## Integration with Other Skills

- **`openapi-analyzer`** - Get endpoint list, validate completeness
- **`endpoint-validator`** - Check translation coverage
- **`translations`** - Propagate to other 29 languages

## File Structure

```
.claude/skills/restapi-translations/
├── SKILL.md                        # This file
├── README.md                       # User documentation
├── QUICKSTART.md                   # Quick start guide
└── scripts/
    ├── extract_keys.py             # Extract keys from code
    ├── validate_translations.py    # Validate translations
    ├── sync_translations.py        # Sync RestApi.php
    ├── manage_translations.sh      # Interactive wrapper
    └── extracted_keys.json         # Generated data
```

## Statistics

Current state:
```
Files scanned:     479 PHP files
Keys found:        2876 total usages
Unique keys:       1589 unique keys

Valid keys:        1370/1589 (86%)
Missing keys:      219 need to be added
Unused keys:       358 could be removed
```

**Goal:** 100% Valid keys (perfect sync)

## Quality Checklist

Before considering complete:
- [ ] Extraction run successfully
- [ ] Validation shows 100% valid keys
- [ ] No missing keys reported
- [ ] Unused keys reviewed
- [ ] All placeholders translated
- [ ] No `[ТРЕБУЕТ ПЕРЕВОДА]` markers
- [ ] PHP syntax is valid
- [ ] Changes committed

## Command Reference

```bash
# Interactive mode
./manage_translations.sh

# Command-line mode
./manage_translations.sh extract
./manage_translations.sh validate
./manage_translations.sh sync --add-missing
./manage_translations.sh sync --remove-unused
./manage_translations.sh all

# Direct Python
python3 extract_keys.py
python3 validate_translations.py
python3 sync_translations.py --add-missing --dry-run
python3 sync_translations.py --add-missing --remove-unused
```

## Success Criteria

Translation management succeeds when:
1. Extraction runs without errors (479 files)
2. Validation shows 100% valid keys
3. No missing keys remain
4. Unused keys reviewed and handled
5. No placeholder markers left
6. PHP syntax valid
7. Git diff clean and meaningful

## Remember

- **Extract before validate** - Validation needs extracted_keys.json
- **Validate before sync** - Know what will change
- **Dry run first** - Preview with --dry-run
- **Translate placeholders** - Don't leave [ТРЕБУЕТ ПЕРЕВОДА]
- **Review unused keys** - Might be for future features
- **Backup is automatic** - RestApi.php.bak.* created
- **PHP validation automatic** - Script checks syntax

## Additional Resources

- **[README.md](README.md)** - Complete documentation
- **[QUICKSTART.md](QUICKSTART.md)** - Quick examples
- **[SUMMARY.md](SUMMARY.md)** - Project summary

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikopbx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
