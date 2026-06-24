---
name: django-celery-task
description: Apply this skill when designing or debugging Celery tasks in Django. Covers task idempotency, retry policies (max_retries, countdown, exponential backoff), error handling patterns, and the anti-pattern of passing ORM objects as task arguments. Triggered by phrases like 'Celery task', '@shared_task', 'retry', 'idempotent', or when adding background processing. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to create a Celery task for async or scheduled work.

## Summary (Human)
Every Celery task needs: queue routing, Redis dedup lock (for snapshot/refresh tasks), timezone-aware dates, multi-tenant processing, and proper error handling.

## Procedure (Claude)

### 1. Define the task with explicit queue
```python
from celery import shared_task
from django.utils import timezone

@shared_task(queue='scheduled')  # ALWAYS specify queue
def check_overdue_payments():
    """Runs daily — checks for overdue payments across all tenants."""
    today = timezone.now().date()  # NEVER date.today()
    # Process all tenants
    for tenant in Tenant.objects.filter(is_active=True):
        _check_tenant_overdue(tenant, today)
```

### 2. Queue routing — match task to queue type
| Queue | Purpose | Examples |
|---|---|---|
| `default` | General tasks, fallback | Email sending |
| `alerts` | Alert processing (latency-sensitive) | Threshold alerts, overdue alerts |
| `scheduled` | Nightly/daily scheduled tasks | Daily summaries, inventory checks |
| `analytics` | Analytics rebuild (off-peak) | Timeseries rebuild, KPI refresh |
| `sync` | Offline sync processing | Batch sync, conflict resolution |
| `snapshots` | Entity snapshot refreshes | Dashboard snapshot rebuild |

### 3. Redis dedup lock (for expensive refresh tasks)
```python
from django.core.cache import cache

@shared_task(queue='snapshots')
def refresh_entity_snapshot(entity_id):
    lock_key = f'snapshot_lock:{entity_id}'
    if not cache.add(lock_key, '1', timeout=300):  # 5-min TTL
        return  # Another task already processing this entity
    try:
        entity = Entity.objects.get(id=entity_id)
        # ... expensive snapshot computation ...
    finally:
        cache.delete(lock_key)  # Always release, even on failure
```

### 4. Multi-tenant processing pattern
```python
@shared_task(queue='scheduled')
def daily_summary_rebuild():
    """Process all active tenants. Fire-and-forget per tenant."""
    for tenant in Tenant.objects.filter(is_active=True):
        try:
            _rebuild_summary_for_tenant(tenant)
        except Exception as e:
            logger.error("Summary rebuild failed for tenant %s: %s",
                        tenant.id, str(e), exc_info=True)
            # Continue to next tenant — don't let one failure block all
```

### 5. NEVER use date.today() or datetime.now()
```python
# WRONG — server timezone may differ from user timezone
from datetime import date
today = date.today()

# CORRECT — timezone-aware
from django.utils import timezone
today = timezone.now().date()
```

### 6. Register in Celery Beat schedule
```python
# config/celery.py or settings
CELERY_BEAT_SCHEDULE = {
    'daily-summary-rebuild': {
        'task': 'apps.myapp.tasks.daily_summary_rebuild',
        'schedule': crontab(hour=23, minute=0),  # 11 PM
        'options': {'queue': 'scheduled'},
    },
}
```
Or use `django-celery-beat` for admin-editable schedules.

## Output
- Task function with explicit queue
- Redis dedup lock for refresh tasks
- Multi-tenant safe (processes all tenants, catches per-tenant errors)
- Timezone-aware dates
- Beat schedule entry

## Edge Cases
- **Task retry:** use `self.retry(exc=e, countdown=60, max_retries=3)` for transient failures
- **Long-running tasks:** set `soft_time_limit` and `time_limit` to prevent zombies
- **Database connections:** Celery workers may have stale connections — use `close_old_connections()`

## Changelog
### v1.0 — 2026-04-02
- Seeded from Shira: 20+ tasks across 6 queues (snapshots, alerts, sync, scheduled, analytics, default)
- Rule #33: worker MUST list ALL queues — missing a queue = tasks stuck forever
- Rule #34: 41 occurrences of date.today() fixed to timezone.now().date()
- Rule #11: Redis dedup lock pattern prevents snapshot thrashing

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
