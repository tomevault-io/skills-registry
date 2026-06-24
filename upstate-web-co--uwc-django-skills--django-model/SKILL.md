---
name: django-model
description: Apply this skill when designing or reviewing Django models. Covers Meta class conventions, field choices patterns, custom managers, abstract base models, and model design principles. Triggered by phrases like 'Django model', 'models.py', 'abstract model', 'Meta class', or when creating new database models. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to create a new Django model for a multi-tenant app.

## Summary (Human)
Every model follows a 5-step process: base class selection → field definition → admin → serializer pairs → ViewSet → tests. Never skip steps.

## Procedure (Claude)

### 1. Select base class(es)
| Base Class | When to Use |
|---|---|
| `TenantAwareModel` | Always (adds tenant FK + timestamps) |
| `AuditableModel(TenantAwareModel)` | Financial records, health records, sensitive data (adds created_by, updated_by) |
| `SoftDeleteMixin` | Primary entities (animals, members, transactions) — never hard-delete |
| `OfflineSyncMixin` | Records that may be created offline (adds client_id UUID) |

```python
class MyRecord(AuditableModel, SoftDeleteMixin):
    tenant = models.ForeignKey('core.Tenant', on_delete=models.CASCADE)
    # fields...
```

### 2. Define fields with correct types
| Data Type | Django Field | Rule |
|---|---|---|
| Money | `DecimalField(max_digits=12, decimal_places=2)` | NEVER float |
| Quantities | `DecimalField(max_digits=10, decimal_places=2)` | With PositiveDecimalValidator |
| Percentages | `DecimalField(max_digits=5, decimal_places=2)` | With PercentageValidator |
| Enums | `models.TextChoices` + `CharField(choices=...)` | Module-level, not inline |
| Dates | `DateField` | Never auto_now for business dates |
| FKs | `ForeignKey(on_delete=CASCADE)` | Add `db_index=True` if filtered often |

### 3. Validators — centralized, never inline
```python
from apps.core.validators import PositiveDecimalValidator, NonNegativeDecimalValidator

amount = models.DecimalField(
    max_digits=12, decimal_places=2,
    validators=[PositiveDecimalValidator()]  # @deconstructible — migration-safe
)
```
ALL validators must be `@deconstructible` when used in model field definitions.

### 4. Meta class
```python
class Meta:
    ordering = ['-created_at']
    indexes = [
        models.Index(fields=['tenant', 'date']),  # common query pattern
    ]
    constraints = [
        models.UniqueConstraint(fields=['tenant', 'name'], name='unique_name_per_tenant'),
    ]
```

### 5. Register admin
```python
@admin.register(MyRecord)
class MyRecordAdmin(TenantScopedAdmin):
    list_display = ['id', 'name', 'date', 'tenant']
    list_filter = ['tenant']
    search_fields = ['name']
```

### 6. Create serializer pairs (never one for both read + write)
```python
class MyRecordListSerializer(BaseTenantSerializer):
    class Meta:
        model = MyRecord
        fields = ['id', 'name', 'date', 'amount']  # minimal for list

class MyRecordReadSerializer(BaseTenantSerializer):
    # Expand FKs for detail view
    related_name = serializers.CharField(source='related.name', read_only=True)
    class Meta:
        model = MyRecord
        fields = ['id', 'name', 'date', 'amount', 'related', 'related_name', 'notes']

class MyRecordWriteSerializer(BaseTenantSerializer):
    # Accept IDs for write
    class Meta:
        model = MyRecord
        fields = ['name', 'date', 'amount', 'related', 'notes']
```
**NEVER** use `fields = '__all__'` — always explicit.

### 7. Create ViewSet
```python
class MyRecordViewSet(BaseTenantViewSet):
    queryset = MyRecord.objects.all()
    serializer_classes = {
        'list': MyRecordListSerializer,
        'retrieve': MyRecordReadSerializer,
        'create': MyRecordWriteSerializer,
        'update': MyRecordWriteSerializer,
    }
    list_select_related = ['related']
    filterset_fields = ['date', 'status']
    search_fields = ['name']
```

### 8. Register URL
```python
router.register('my-records', MyRecordViewSet, basename='my-record')
```

### 9. Write 5 mandatory tests
```python
def test_unauthenticated_returns_401(self): ...
def test_own_tenant_returns_200(self): ...
def test_cross_tenant_returns_404(self): ...  # NOT 403
def test_missing_required_field_returns_400(self): ...
def test_list_query_count(self):
    with self.assertNumQueries(N): ...  # catch N+1
```

## Output
- Model with correct base classes and validators
- Admin registration
- 3 serializers (List/Read/Write)
- ViewSet with serializer dispatch
- URL registration
- 5+ tests

## Edge Cases
- **Soft-deleted records**: default manager filters `is_active=True`. Use `AllObjectsManager` for admin recovery.
- **Computed fields**: add as `@property` on model, expose as `SerializerMethodField` on read serializer
- **Unique constraints**: always scope to tenant (not globally unique)

## Changelog
### v1.0 — 2026-04-02
- Initial version, seeded from Shira's `/new-model` skill
- 90+ models built following this exact pattern
- Key lessons: always `@deconstructible` validators (BGF-006), never `fields = '__all__'`, always test cross-tenant 404

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
