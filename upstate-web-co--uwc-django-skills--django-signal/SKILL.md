---
name: django-signal
description: Apply this skill when writing or reviewing Django signals. Covers dispatch_uid for idempotency, the mandatory `created` check in post_save receivers, atomic transaction awareness, single-responsibility receivers, and the anti-pattern of putting business logic in signals. Triggered by phrases like 'Django signal', 'post_save', 'receiver', 'dispatch_uid', or when adding signal handlers to a Django project. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to create a Django signal receiver (post_save, post_delete, etc.).

## Summary (Human)
Signals are the #1 source of bugs in Django apps. Every receiver MUST have: dispatch_uid, created check, terminal status guard, transaction.atomic (if mutating), and single responsibility.

## Procedure (Claude)

### 1. ALWAYS use dispatch_uid
```python
@receiver(post_save, sender=MyModel, dispatch_uid='myapp_mymodel_do_thing')
def handle_mymodel_saved(sender, instance, created, **kwargs):
```
Without dispatch_uid, Django can register the same receiver twice → double execution.

### 2. Check `created` kwarg — skip updates unless intentional
```python
def handle_mymodel_saved(sender, instance, created, **kwargs):
    if not created:
        return  # Only fire on CREATE, not UPDATE
```
Most signals should only fire on creation. If you need update behavior, be explicit about which fields trigger it.

### 3. Terminal status guard (for stateful entities)
```python
TERMINAL_STATUSES = {'SOLD', 'DEAD', 'CULLED', 'CLOSED', 'ARCHIVED'}

def handle_entity_saved(sender, instance, created, **kwargs):
    if not created:
        return
    if hasattr(instance, 'status') and instance.status in TERMINAL_STATUSES:
        return  # Don't create downstream records for terminal entities
```

### 4. Use transaction.atomic for inventory/financial mutations
```python
from django.db import transaction

def handle_purchase_saved(sender, instance, created, **kwargs):
    if not created:
        return
    with transaction.atomic():
        inventory = (
            Inventory.objects
            .select_for_update()
            .get_or_create(tenant=instance.tenant, item=instance.item)[0]
        )
        inventory.stock += instance.quantity
        inventory.save(update_fields=['stock', 'updated_at'])
```
**Critical:** Always `select_for_update()` when mutating shared state. Without it, concurrent writes cause race conditions.

### 5. Single responsibility per receiver
```python
# WRONG — one receiver doing 3 things
def handle_sale(sender, instance, created, **kwargs):
    update_inventory(instance)
    create_transaction(instance)
    send_notification(instance)

# CORRECT — three receivers, each doing one thing
@receiver(post_save, sender=Sale, dispatch_uid='sale_update_inventory')
def update_inventory_on_sale(sender, instance, created, **kwargs): ...

@receiver(post_save, sender=Sale, dispatch_uid='sale_create_transaction')
def create_transaction_on_sale(sender, instance, created, **kwargs): ...

@receiver(post_save, sender=Sale, dispatch_uid='sale_notify')
def notify_on_sale(sender, instance, created, **kwargs): ...
```

### 6. Business logic in services, not signals
```python
# WRONG — logic in signal
def handle_sale(sender, instance, created, **kwargs):
    profit = instance.price - instance.cost  # business logic in signal

# CORRECT — signal calls service
from apps.myapp.services.sale_service import record_sale_profit

def handle_sale(sender, instance, created, **kwargs):
    if not created:
        return
    record_sale_profit(instance)
```

### 7. Clamp-at-zero guard for stock deductions
```python
# When deducting from inventory, clamp to zero — never go negative
inventory.stock = max(Decimal('0'), inventory.stock - deduction_amount)
inventory.save(update_fields=['stock', 'updated_at'])
```

### 8. Split signal files by domain
```
myapp/
├── signals/
│   ├── __init__.py          # import all signal modules
│   ├── sale_signals.py
│   ├── inventory_signals.py
│   └── notification_signals.py
```
Register in `apps.py`: `def ready(self): import apps.myapp.signals`

## Output
- Signal receiver with all guardrails
- Tests: no-fire-on-update, skip-terminal-status, idempotency, happy-path

## Edge Cases
- **get_or_create in signal**: always returns (obj, created) — handle both cases
- **Signal firing during migration**: use `if not getattr(instance, '_skip_signals', False)` for data migrations
- **Circular signals**: A saves B, B's signal saves A → infinite loop. Use `_skip_signals` flag or check specific field changes

## Test Template
```python
def test_signal_fires_on_create(self):
    record = MyModel.objects.create(...)
    assert DownstreamModel.objects.filter(...).exists()

def test_signal_skips_on_update(self):
    record = MyModel.objects.create(...)
    initial_count = DownstreamModel.objects.count()
    record.some_field = 'new_value'
    record.save()
    assert DownstreamModel.objects.count() == initial_count  # no new records

def test_signal_skips_terminal_status(self):
    record = MyModel.objects.create(status='SOLD', ...)
    assert not DownstreamModel.objects.filter(...).exists()

def test_signal_idempotent(self):
    # Manually call handler twice — should not create duplicates
    handle_mymodel_saved(sender=MyModel, instance=record, created=True)
    handle_mymodel_saved(sender=MyModel, instance=record, created=True)
    assert DownstreamModel.objects.count() == 1
```

## Changelog
### v1.0 — 2026-04-02
- Initial version, seeded from Shira's `/new-signal` skill
- Encodes lessons from 54 bugs: signals were the #1 bug category
- BGF-005: receiver firing on UPDATE (missing created check)
- BGF-053: feeding signal missing stock deduction (no atomic, no select_for_update)
- BGF-054: race condition in swine feeding (no transaction.atomic)
- Shira used 50+ signal receivers across 15 signal files — all follow this pattern

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
