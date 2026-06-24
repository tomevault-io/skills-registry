---
name: django-migration
description: Apply this skill when writing or reviewing Django migrations. Covers data migrations with RunPython, @deconstructible for custom field arguments, table lock avoidance strategies, and migration safety checks. Triggered by phrases like 'Django migration', 'makemigrations', 'RunPython', 'data migration', or when modifying database schema. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to change a Django model schema, especially on models with existing data.

## Summary (Human)
Migrations on live data are dangerous. Adding a non-nullable column without a default locks the table. Dropping a column before removing code causes errors. Always: null first → backfill → constrain.

## Procedure (Claude)

### 1. Before running makemigrations, check for risks

| Change | Risk | Mitigation |
|---|---|---|
| Add non-nullable column | **TABLE LOCK** — blocks all reads/writes | Add with `null=True`, backfill, then remove null |
| Add nullable column | Safe | Direct migration |
| Remove column | **Code error** if code still references it | Remove code first, then column in next deploy |
| Rename column | **Data loss risk** with naive approach | Use `RenameField` operation, never drop+add |
| Add index | Can be slow on large tables | Use `AddIndex` with `concurrently=True` on PostgreSQL |
| Add unique constraint | Fails if duplicates exist | Backfill/dedupe first, then add constraint |

### 2. Three-step pattern for non-nullable columns
```python
# Migration 1: Add nullable
class Migration(migrations.Migration):
    operations = [
        migrations.AddField('MyModel', 'new_field',
            models.CharField(max_length=100, null=True, blank=True)),
    ]

# Migration 2: Backfill data
class Migration(migrations.Migration):
    operations = [
        migrations.RunPython(backfill_new_field, migrations.RunPython.noop),
    ]

# Migration 3: Remove null
class Migration(migrations.Migration):
    operations = [
        migrations.AlterField('MyModel', 'new_field',
            models.CharField(max_length=100, default='unknown')),
    ]
```

### 3. Data migrations for enum/status changes
```python
def migrate_status_values(apps, schema_editor):
    MyModel = apps.get_model('myapp', 'MyModel')
    MyModel.objects.filter(status='OLD_VALUE').update(status='NEW_VALUE')
```

### 4. Validators must be @deconstructible
```python
from django.utils.deconstruct import deconstructible

@deconstructible
class PositiveDecimalValidator:
    def __call__(self, value):
        if value <= 0:
            raise ValidationError('Must be positive.')
```
Without `@deconstructible`, `makemigrations` crashes with `ValueError: Cannot serialize`.

### 5. Pre-migration checklist
- [ ] `python manage.py makemigrations --dry-run` — preview changes
- [ ] `python manage.py migrate --plan` — check dependency order
- [ ] Check for table locks (non-nullable without default?)
- [ ] Check for data migration needs (enum changes, backfills?)
- [ ] Run tests against migration: `pytest --tb=short`
- [ ] Backup database before applying to production

### 6. Zero-downtime deployment order
1. Deploy migration (adds nullable column or new table)
2. Deploy code that uses new column (reads/writes)
3. Deploy migration that removes null constraint (after data fills)
4. Deploy migration that removes old column (after code no longer references it)

## Output
- Safe migration file(s)
- Data migration if needed
- All validators @deconstructible

## Edge Cases
- **ForeignKey additions:** target table must exist first — check migration dependency order
- **Unique constraints with existing duplicates:** must deduplicate before adding constraint
- **Default values:** use `django.utils.timezone.now` for dates, never `datetime.now`

## Changelog
### v1.0 — 2026-04-02
- Seeded from Shira: BGF-006 (@deconstructible missing crashed makemigrations)
- Shira Rule #15: never migrate live data without review
- Shira Phase 6 audit: 41 occurrences of `date.today()` fixed in migrations to `timezone.now().date()`

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
