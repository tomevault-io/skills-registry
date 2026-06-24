---
name: django-service
description: Apply this skill when organizing business logic in Django. Covers the service layer pattern — extracting business logic from views and models into dedicated service classes for testability, reusability, and separation of concerns. Triggered by phrases like 'service layer', 'business logic', 'fat models', 'where to put logic', or when refactoring Django views. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to add business logic that doesn't belong in a model, view, or signal.

## Summary (Human)
Business logic lives in `[app]/services/[domain].py`. Models are pure data. Views handle HTTP. Signals call services. Services contain the rules.

## Procedure (Claude)

### 1. Create service module
```
myapp/
├── services/
│   ├── __init__.py
│   └── order_service.py      ← one file per domain
```

### 2. Write stateless functions (not classes, unless caching)
```python
# myapp/services/order_service.py
from decimal import Decimal
from django.db import transaction
from django.utils import timezone

def calculate_order_total(order):
    """Pure calculation — no side effects."""
    return sum(item.quantity * item.unit_price for item in order.items.all())

def complete_order(order, completed_by):
    """State transition with side effects — uses atomic."""
    with transaction.atomic():
        order.status = 'COMPLETED'
        order.completed_at = timezone.now()
        order.completed_by = completed_by
        order.total = calculate_order_total(order)
        order.save(update_fields=['status', 'completed_at', 'completed_by', 'total', 'updated_at'])
        # Create financial record
        Transaction.objects.create(
            tenant=order.tenant,
            amount=order.total,
            transaction_type='INCOME',
            description=f'Order {order.reference}',
        )
    return order
```

### 3. For cached lookups, use a service class
```python
# core/services/settings_service.py
from django.core.cache import cache

class TenantSettingsService:
    CACHE_TTL = 300  # 5 minutes

    @staticmethod
    def get_threshold(tenant, field_name):
        cache_key = f'tenant_settings:{tenant.id}:{field_name}'
        value = cache.get(cache_key)
        if value is None:
            value = getattr(tenant.settings, field_name)
            cache.set(cache_key, value, TenantSettingsService.CACHE_TTL)
        return value

    @staticmethod
    def invalidate(tenant):
        # Called from TenantSettings post_save signal
        cache.delete_pattern(f'tenant_settings:{tenant.id}:*')
```

### 4. Signals call services — never the reverse
```python
# CORRECT: signal → service
@receiver(post_save, sender=Sale, dispatch_uid='sale_record_revenue')
def record_revenue_on_sale(sender, instance, created, **kwargs):
    if not created:
        return
    from apps.finance.services.revenue_service import record_sale_revenue
    record_sale_revenue(instance)

# WRONG: service importing signal
def record_sale_revenue(sale):
    from apps.myapp.signals import trigger_notification  # circular, fragile
```

### 5. Views call services for complex operations
```python
class OrderViewSet(BaseTenantViewSet):
    @action(detail=True, methods=['post'])
    def complete(self, request, pk=None):
        order = self.get_object()
        order = complete_order(order, completed_by=request.user)
        return Response(OrderReadSerializer(order).data)
```

## Output
- Service module in `[app]/services/[domain].py`
- Stateless functions for pure logic, class for cached lookups
- Signals and views call services, not the reverse

## Edge Cases
- **Circular imports:** use late imports inside functions (`from apps.x.services.y import z`)
- **Testing services:** test service functions directly, not through signals/views
- **Tenant context:** pass tenant explicitly to service functions, don't rely on request context

## Changelog
### v1.0 — 2026-04-02
- Seeded from Shira (12+ service modules: milk, breeding, health, billing, scheduling, economics, growth, inventory, KPI, settings, team, email verification)
- Seeded from MyChama (MpesaDarajaAPI, PlatformBillingService, NotificationService, MessagingService)

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
