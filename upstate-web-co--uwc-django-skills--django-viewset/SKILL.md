---
name: django-viewset
description: Apply this skill when building or reviewing Django REST Framework ViewSets. Covers action decorators, queryset optimization with select_related/prefetch_related, permission classes, pagination, and the anti-pattern of putting business logic in views. Triggered by phrases like 'DRF ViewSet', 'ModelViewSet', 'action decorator', or when building API endpoints. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to create a new DRF ViewSet for a multi-tenant app.

## Summary (Human)
Every ViewSet inherits `BaseTenantViewSet`, declares `serializer_classes` dict + `list_select_related`/`detail_select_related`, and never overrides `get_queryset` unless the model has an indirect tenant FK. The base class handles: tenant filtering, subscription checks, module feature flags, role permissions, and auto-injecting tenant + user on create.

## Procedure (Claude)

### 1. Inherit BaseTenantViewSet (never raw ModelViewSet)

```python
from apps.core.viewsets import BaseTenantViewSet

class CowViewSet(BaseTenantViewSet):
    queryset = Cow.objects.all()
```

The base class provides:
- **Tenant auto-filtering** via `FarmScopedMixin.get_queryset()` — all queries filtered to `request.farm`
- **Subscription enforcement** — returns 402 if tenant subscription is inactive
- **Module feature flags** — returns 403 if the module (e.g., `dairy_enabled`) is disabled for the tenant
- **Role permissions** — checks `RoleModulePermission` per action
- **Auto tenant/user injection** — `perform_create` sets `farm=farm, created_by=request.user`
- **Soft delete** — `perform_destroy` calls `instance.delete(deleted_by=request.user)`

### 2. Declare serializer_classes dict (never override get_serializer_class)

```python
class CowViewSet(BaseTenantViewSet):
    queryset = Cow.objects.all()
    serializer_classes = {
        'list': CowListSerializer,
        'retrieve': CowDetailSerializer,
        'create': CowWriteSerializer,
        'update': CowWriteSerializer,
        'partial_update': CowWriteSerializer,
        'default': CowListSerializer,
    }
```

The base class resolves the serializer by `self.action` key. Always provide at minimum `list`, `retrieve`, `create`, and `default`. Read serializers expand FKs; write serializers accept IDs.

### 3. Declare select_related / prefetch_related as class attributes

```python
class CowViewSet(BaseTenantViewSet):
    queryset = Cow.objects.all()
    serializer_classes = { ... }

    # List views: minimal joins for table/card display
    list_select_related = ['production_snapshot']
    list_prefetch_related = []

    # Detail views: full joins for single-record display
    detail_select_related = [
        'mother', 'father', 'production_snapshot',
        'reproduction_snapshot', 'health_snapshot',
    ]
    detail_prefetch_related = ['versions__components__feed_type']
```

The base class applies these automatically based on `self.action == 'list'` vs detail actions. Never call `.select_related()` manually inside `get_queryset` unless you also need to override the queryset logic.

### 4. Add filtering, search, and ordering

```python
class CowViewSet(BaseTenantViewSet):
    queryset = Cow.objects.all()
    serializer_classes = { ... }

    filterset_class = CowFilter          # django-filter FilterSet
    search_fields = ['name', 'tag_id']   # DRF SearchFilter
    ordering_fields = ['name', 'tag_id', 'date_of_birth', 'current_status']
    ordering = ['name', 'tag_id']        # default ordering
```

Filter backends are configured globally in `REST_FRAMEWORK` settings:
```python
'DEFAULT_FILTER_BACKENDS': [
    'django_filters.rest_framework.DjangoFilterBackend',
    'rest_framework.filters.SearchFilter',
    'rest_framework.filters.OrderingFilter',
],
'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
'PAGE_SIZE': 50,
```

### 5. Custom actions

```python
# Detail action — operates on a single record
@action(detail=True, methods=['get'], url_path='summary')
def summary(self, request, pk=None):
    cow = self.get_object()  # auto-filtered to tenant
    summary_data = CowSummaryService.get_summary(cow)
    return Response(summary_data)

# List action — operates on the filtered queryset
@action(detail=False, methods=['post'], url_path='reconcile')
def reconcile(self, request):
    serializer = ReconciliationWriteSerializer(
        data=request.data, context={'request': request}
    )
    serializer.is_valid(raise_exception=True)
    farm = self._resolve_farm()
    results = reconcile_inventory(farm=farm, **serializer.validated_data)
    return Response(results)
```

### 6. Override perform_create when extra fields needed

```python
def perform_create(self, serializer):
    farm = self._resolve_farm()
    serializer.save(
        farm=farm,
        recorded_by=self.request.user,
        created_by=self.request.user,
    )
```

### 7. Restrict HTTP methods when appropriate

```python
# Signal-managed model — read only
class CashBalanceViewSet(FarmScopedMixin, viewsets.ReadOnlyModelViewSet):
    queryset = CashBalance.objects.all()
    serializer_class = CashBalanceListSerializer

# Permanent records — no update/delete
class CowExitViewSet(BaseTenantViewSet):
    http_method_names = ['get', 'post', 'head', 'options']
```

### 8. Indirect tenant FK — override get_queryset

Only when the model doesn't have a direct tenant FK:

```python
class PoultryBatchSnapshotViewSet(BaseTenantViewSet):
    def get_queryset(self):
        farm = self._resolve_farm()
        if farm:
            return PoultryBatchSnapshot.objects.filter(flock_batch__farm=farm)
        return PoultryBatchSnapshot.objects.none()

class FeedMixVersionViewSet(BaseTenantViewSet):
    def get_queryset(self):
        qs = FeedMixVersion.objects.select_related('feed_mix').prefetch_related(
            'components__feed_type'
        )
        farm = self._resolve_farm()
        if farm:
            return qs.filter(feed_mix__farm=farm)
        return qs.none()
```

## BaseTenantViewSet Reference

```python
class FarmScopedMixin:
    """Auto-filters all querysets to request.farm. Returns 404 for cross-tenant."""

    def _resolve_farm(self):
        farm = getattr(self.request, 'farm', None)
        if farm:
            return farm
        user = getattr(self.request, 'user', None)
        if user and user.is_authenticated and hasattr(user, 'farm_id'):
            from apps.core.models import Farm
            try:
                farm = Farm.objects.get(pk=user.farm_id)
                self.request.farm = farm
                return farm
            except Farm.DoesNotExist:
                pass
        return None

    def get_queryset(self):
        qs = super().get_queryset()
        farm = self._resolve_farm()
        if farm:
            return qs.filter(farm=farm)
        return qs.none()


class BaseTenantViewSet(FarmScopedMixin, viewsets.ModelViewSet):
    serializer_classes: dict = {}
    list_select_related: list = []
    list_prefetch_related: list = []
    detail_select_related: list = []
    detail_prefetch_related: list = []
    module_permission: str | None = None

    def get_serializer_class(self):
        if self.action in self.serializer_classes:
            return self.serializer_classes[self.action]
        if 'default' in self.serializer_classes:
            return self.serializer_classes['default']
        return super().get_serializer_class()

    def get_queryset(self):
        qs = super().get_queryset()
        if self.action == 'list':
            if self.list_select_related:
                qs = qs.select_related(*self.list_select_related)
            if self.list_prefetch_related:
                qs = qs.prefetch_related(*self.list_prefetch_related)
        else:
            if self.detail_select_related:
                qs = qs.select_related(*self.detail_select_related)
            if self.detail_prefetch_related:
                qs = qs.prefetch_related(*self.detail_prefetch_related)
        return qs

    def perform_create(self, serializer):
        farm = self._resolve_farm()
        serializer.save(farm=farm, created_by=self.request.user)

    def perform_update(self, serializer):
        serializer.save(updated_by=self.request.user)

    def perform_destroy(self, instance):
        instance.delete(deleted_by=self.request.user)
```

## Anti-Patterns

| Anti-Pattern | Correct |
|---|---|
| Inherit raw `ModelViewSet` | Always `BaseTenantViewSet` |
| Override `get_serializer_class` manually | Use `serializer_classes` dict |
| Call `.select_related()` in `get_queryset` | Use class attributes `list_select_related` / `detail_select_related` |
| Return 403 for cross-tenant resources | Return 404 (base class does this) |
| Inline permission checks in actions | Use `permission_classes` or `RoleModulePermission` |
| Call `.delete()` on financial/animal records | Use `perform_destroy` → soft delete |
| Use `.objects.all()` without tenant filter | Base class handles this automatically |
| Override `get_queryset` for direct tenant FK | Only override for indirect FK (`flock_batch__farm`) |
| Use analytics ViewSet without full base class | Must use `BaseTenantViewSet` (not just `FarmScopedMixin`) for subscription + module + role checks |

## Source
- Shira: `apps/core/viewsets.py` (BaseFarmViewSet), 40+ ViewSets across 11 apps
- MyChama: `playground/views.py` (OrganizationFilteredViewSet)

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
