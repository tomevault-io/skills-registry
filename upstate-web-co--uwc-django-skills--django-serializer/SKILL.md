---
name: django-serializer
description: Apply this skill when building or reviewing DRF serializers. Covers SerializerMethodField, nested writes, read-only fields, validation patterns (field-level vs object-level), and serializer context. Triggered by phrases like 'DRF serializer', 'SerializerMethodField', 'nested serializer', 'validate', or when serializing complex Django models. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to create DRF serializers for a model.

## Summary (Human)
Always create 3 serializers per model: List (minimal), Read (expanded FKs), Write (accepts IDs). Never use `fields = '__all__'`. Never use one serializer for both read and write.

## Procedure (Claude)

### 1. List serializer (minimal fields for table views)
```python
class OrderListSerializer(BaseTenantSerializer):
    customer_name = serializers.CharField(source='customer.name', read_only=True)

    class Meta:
        model = Order
        fields = ['id', 'reference', 'date', 'customer', 'customer_name', 'total', 'status']
```

### 2. Read serializer (expanded FKs for detail views)
```python
class OrderReadSerializer(BaseTenantSerializer):
    customer_name = serializers.CharField(source='customer.name', read_only=True)
    customer_phone = serializers.CharField(source='customer.phone', read_only=True)
    items = OrderItemSerializer(many=True, read_only=True)
    total_display = serializers.SerializerMethodField()

    class Meta:
        model = Order
        fields = ['id', 'reference', 'date', 'customer', 'customer_name',
                  'customer_phone', 'items', 'total', 'total_display', 'status', 'notes']

    def get_total_display(self, obj):
        return f'{obj.total:,.2f}'
```

### 3. Write serializer (accepts IDs, validates)
```python
class OrderWriteSerializer(BaseTenantSerializer):
    class Meta:
        model = Order
        fields = ['date', 'customer', 'notes']  # only writable fields

    def validate_date(self, value):
        if value > timezone.now().date():
            raise serializers.ValidationError('Date cannot be in the future.')
        return value
```

### 4. Wire into ViewSet
```python
class OrderViewSet(BaseTenantViewSet):
    serializer_classes = {
        'list': OrderListSerializer,
        'retrieve': OrderReadSerializer,
        'create': OrderWriteSerializer,
        'update': OrderWriteSerializer,
        'partial_update': OrderWriteSerializer,
    }
```

### 5. BaseTenantSerializer auto-validation
The base serializer should auto-validate:
- **FK tenant ownership:** every FK field's object must belong to same tenant
- **Future date rejection:** DateField/DateTimeField rejected if future (whitelist exceptions via `allow_future_date_fields`)
- **Excluded system fields:** tenant, created_at, updated_at, created_by, updated_by

## Rules
- **NEVER** `fields = '__all__'` — exposes system fields, no role control
- **NEVER** one serializer for read + write — read expands FKs, write accepts IDs
- **Field names must be snake_case** — must match frontend Zod schema exactly
- **Computed fields** use `SerializerMethodField` on read serializer only
- **Nested writes** are generally bad — use separate endpoints or service functions
- **(v1.1) The frontend Zod schema MUST mirror this serializer field-by-field** — see skill-frontend-backend-contract-sync.md for the contract. Per Astro+DRF stack, each serializer needs a matching XListSchema or XDetailSchema in `frontend/src/lib/schemas.ts` annotated with `// Mirrors apps/X/serializers.py::Y`. NO defensive `.optional()` on a field this serializer's `Meta.fields` includes
- **(v1.1) "List = lean" is a default, not a law** — if the frontend UI displays detail-grade fields inline on a list page (e.g., property detail page rendering draft body inline), extend the ListSerializer to include them. UI shape drives backend list shape, not the other way around. Document the deviation with a comment on the serializer (BGF-003 carry-forward)

## Frontend mirror — field-type cross-check (v1.1)

When mirroring a serializer to a frontend Zod schema, you MUST cross-check
field TYPES against `apps/X/models.py` — `serializers.py` only gives you
field NAMES. The two together give the wire shape; either alone will mislead
you. Hale's BGF-005 was 4 fields with right names but wrong Zod types.

| Django field declaration | Wire format (DRF default) | Zod type |
|---|---|---|
| `CharField`, `TextField`, `URLField`, `EmailField` | string | `z.string()` (or `.email()` / `.url()` for strict) |
| `BooleanField()` | `true` / `false` | `z.boolean()` |
| `BooleanField(null=True)` | `true` / `false` / `null` | `z.boolean().nullable()` |
| `IntegerField()` | number | `z.number()` |
| `IntegerField(null=True)` | number / null | `z.number().nullable()` |
| `DecimalField` | string (DRF serializes Decimal as string) | `z.string()` |
| `DecimalField(null=True)` | string / null | `z.string().nullable()` |
| `DateTimeField`, `DateField` | ISO 8601 string | `z.string()` |
| `JSONField(default=dict)` | object `{}` | `z.record(z.unknown())` |
| `JSONField(default=list)` of objects | array of objects | `z.array(z.record(z.unknown()))` |
| `JSONField(default=list)` of strings | array of strings | `z.array(z.string())` |
| `ForeignKey` | UUID string (or numeric id) | `Uuid` |
| `TextChoices` enum | string from finite set | `z.enum([...])` matching the choices |

**Audit step before merging any backend serializer change:**

1. Open both `apps/X/serializers.py` and `apps/X/models.py` side-by-side
2. For each field in `Meta.fields`, find the model declaration
3. Confirm the matching frontend Zod field uses the right type from the table
4. Run `tests/e2e/contract.spec.ts` (per skill-frontend-backend-contract-sync) — if any safeParse fails, fix the schema before merge

**TextChoices carry-forward (BGF-001):** when adding a value to a Django
`TextChoices`, mirror in the frontend Zod enum in the SAME PR. Older rows
with deprecated values must either be migrated, or both old + new values
must remain in the enum until cleanup runs. Never drop an enum value the DB
still contains.

## Output
- 3 serializers (List/Read/Write) per model
- BaseTenantSerializer with auto-validation
- ViewSet `serializer_classes` dict wired up

## Changelog
### v1.1 — 2026-04-26
- Adds the frontend-mirror discipline: every serializer needs a matching
  Zod schema in `frontend/src/lib/schemas.ts` per
  skill-frontend-backend-contract-sync.md
- Adds the model-field-type cross-check table (BooleanField → z.boolean.nullable,
  IntegerField → z.number.nullable, JSONField default=dict → z.record,
  JSONField default=list of objects → z.array(z.record))
- Adds the BGF-003 deviation rule: "List = lean" is a default, not a law —
  if UI displays detail-grade fields inline on a list page, extend the
  ListSerializer; document the deviation
- Adds the BGF-001 enum carry-forward: TextChoices values must be mirrored
  in the frontend Zod enum in the SAME PR; never drop an enum value the DB
  still contains
- Source: Hale BGF-001/002/003/005 cascade 2026-04-26 — 4 schema-drift
  failures in 90 minutes after launch. Pattern is structural for any
  Astro+DRF UWC project

### v1.0 — 2026-04-02
- Seeded from Shira: 180+ endpoints, all follow this triple-serializer pattern
- Key lesson: `fields = '__all__'` caused 3 bugs by exposing system fields
- Key lesson: FK tenant ownership validation prevents cross-tenant data injection

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
