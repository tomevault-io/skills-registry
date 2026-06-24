---
name: django-backend-best-practices
description: Best practices for Django backend service development. Covers project architecture, models, querysets, serializers, views, error handling, security, performance, and production readiness. Use when this capability is needed.
metadata:
  author: zulfafalah
---

# Django Backend Service — Best Practices

Apply these best practices when developing Django as a backend API service.

---

## 1. Project Architecture

### Fat Models, Thin Views

- Put business logic in **models** or **service layers** — never in views or serializers.
- Views should only handle request/response flow: parse input → call business logic → return output.

```python
# GOOD — logic in model
class Order(models.Model):
    def cancel(self):
        if self.status == "shipped":
            raise ValidationError("Cannot cancel shipped order.")
        self.status = "cancelled"
        self.cancelled_at = timezone.now()
        self.save(update_fields=["status", "cancelled_at"])

# GOOD — logic in service layer for complex operations
# app/services.py
class OrderService:
    @staticmethod
    def create_order(user, items, shipping_address):
        with transaction.atomic():
            order = Order.objects.create(user=user, address=shipping_address)
            OrderItem.objects.bulk_create([
                OrderItem(order=order, **item) for item in items
            ])
            InventoryService.reserve_stock(items)
            return order

# BAD — logic in view
class OrderViewSet(ModelViewSet):
    def perform_create(self, serializer):
        # Don't put complex logic here
        ...
```

### Service Layer (for complex operations)

Use a `services.py` file when business logic:
- Spans multiple models or apps
- Requires transactions across tables
- Involves external API calls
- Is too complex for a single model method

```
app_name/
├── api/
│   ├── serializers.py
│   └── views.py
├── models.py
├── services.py          # Complex business logic
├── selectors.py         # Complex read queries (optional)
├── tasks.py             # Celery async tasks
└── tests/
```

---

## 2. Models Best Practices

### Use Abstract Base Models for Common Fields

```python
class TimestampedModel(models.Model):
    """Abstract base with created/updated timestamps."""
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True


class Order(TimestampedModel):
    # Inherits created_at and updated_at automatically
    order_number = models.CharField(max_length=50, unique=True)
```

### Field Definitions

- Always set `max_length` on `CharField`.
- Use `DecimalField` for money — never `FloatField`.
- Use `TextChoices` / `IntegerChoices` for status fields:

```python
class Order(models.Model):
    class Status(models.TextChoices):
        DRAFT = "draft", "Draft"
        CONFIRMED = "confirmed", "Confirmed"
        SHIPPED = "shipped", "Shipped"
        CANCELLED = "cancelled", "Cancelled"

    status = models.CharField(
        max_length=20,
        choices=Status.choices,
        default=Status.DRAFT,
    )
```

### Indexes & Constraints

- Add `db_index=True` on fields you filter/order by frequently.
- Use `Meta.indexes` for composite indexes.
- Use `Meta.constraints` for database-level validation:

```python
class Meta:
    indexes = [
        models.Index(fields=["status", "-created_at"]),
    ]
    constraints = [
        models.UniqueConstraint(
            fields=["user", "product"],
            name="unique_user_product",
        ),
        models.CheckConstraint(
            condition=models.Q(quantity__gte=0),
            name="quantity_non_negative",
        ),
    ]
```

---

## 3. QuerySet Best Practices

### Avoid N+1 Queries

```python
# BAD — N+1 queries
orders = Order.objects.all()
for order in orders:
    print(order.customer.name)  # Hits DB each iteration

# GOOD — select_related for ForeignKey/OneToOne
orders = Order.objects.select_related("customer").all()

# GOOD — prefetch_related for ManyToMany/reverse FK
orders = Order.objects.prefetch_related("items", "items__product").all()
```

### Use QuerySet Methods Efficiently

```python
# Use .only() / .defer() to limit fields
Order.objects.only("id", "status", "total").filter(status="draft")

# Use .values() / .values_list() for raw data without model instantiation
Order.objects.values_list("id", flat=True).filter(status="draft")

# Use .exists() instead of .count() for boolean checks
if Order.objects.filter(user=user, status="draft").exists():
    ...

# Use .update() for bulk updates — avoids loading each instance
Order.objects.filter(status="draft", created_at__lt=cutoff).update(status="expired")

# Use bulk_create / bulk_update for batch operations
OrderItem.objects.bulk_create(items, batch_size=1000)
```

### Custom Managers & QuerySets

```python
class OrderQuerySet(models.QuerySet):
    def active(self):
        return self.exclude(status="cancelled")

    def overdue(self):
        return self.filter(
            status="confirmed",
            due_date__lt=timezone.now(),
        )


class Order(models.Model):
    objects = OrderQuerySet.as_manager()

# Usage: Order.objects.active().overdue()
```

---

## 4. Serializer Best Practices

### Separate Read vs Write Serializers

```python
class OrderListSerializer(serializers.ModelSerializer[Order]):
    """Lightweight serializer for list endpoint."""
    customer_name = serializers.CharField(source="customer.name", read_only=True)

    class Meta:
        model = Order
        fields = ["id", "order_number", "customer_name", "status", "total", "created_at"]


class OrderDetailSerializer(serializers.ModelSerializer[Order]):
    """Full serializer with nested items for detail endpoint."""
    items = OrderItemSerializer(many=True, read_only=True)
    customer = CustomerSerializer(read_only=True)

    class Meta:
        model = Order
        fields = ["id", "order_number", "customer", "items", "status", "total", "notes", "created_at"]


class OrderCreateSerializer(serializers.ModelSerializer[Order]):
    """Write serializer for creating orders."""
    items = OrderItemWriteSerializer(many=True)

    class Meta:
        model = Order
        fields = ["customer", "items", "notes", "shipping_address"]

    def create(self, validated_data):
        items_data = validated_data.pop("items")
        order = Order.objects.create(**validated_data)
        OrderItem.objects.bulk_create([
            OrderItem(order=order, **item) for item in items_data
        ])
        return order
```

### Validation

```python
class OrderCreateSerializer(serializers.Serializer):
    # Field-level validation
    def validate_quantity(self, value):
        if value <= 0:
            raise serializers.ValidationError("Quantity must be positive.")
        return value

    # Cross-field validation
    def validate(self, attrs):
        if attrs["start_date"] > attrs["end_date"]:
            raise serializers.ValidationError("start_date must be before end_date.")
        return attrs
```

- Never use `fields = "__all__"` — always list fields explicitly.
- Use `read_only_fields` for auto-generated fields.
- Use `source` for renaming fields in API output.

---

## 5. ViewSet & View Best Practices

### Use get_serializer_class for Multi-Serializer ViewSets

```python
class OrderViewSet(ModelViewSet):
    queryset = Order.objects.all()

    def get_serializer_class(self):
        if self.action == "list":
            return OrderListSerializer
        if self.action == "retrieve":
            return OrderDetailSerializer
        if self.action in ("create", "update", "partial_update"):
            return OrderCreateSerializer
        return OrderDetailSerializer

    def get_queryset(self):
        qs = super().get_queryset()
        if self.action == "list":
            return qs.select_related("customer").only(
                "id", "order_number", "status", "total", "created_at",
                "customer__name",
            )
        return qs.select_related("customer").prefetch_related("items__product")
```

### Filtering, Searching, Ordering

```python
class OrderViewSet(ModelViewSet):
    filterset_fields = ["status", "customer"]
    search_fields = ["order_number", "customer__name"]
    ordering_fields = ["created_at", "total"]
    ordering = ["-created_at"]  # default ordering
```

### Pagination

Configure globally in settings or per-view:

```python
# settings/base.py
REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS": "rest_framework.pagination.PageNumberPagination",
    "PAGE_SIZE": 25,
}

# Or custom per-view
from rest_framework.pagination import PageNumberPagination

class LargeResultPagination(PageNumberPagination):
    page_size = 100
    page_size_query_param = "page_size"
    max_page_size = 500

class OrderViewSet(ModelViewSet):
    pagination_class = LargeResultPagination
```

---

## 6. Error Handling

### Use DRF Exceptions Consistently

```python
from rest_framework.exceptions import (
    NotFound,
    PermissionDenied,
    ValidationError,
)

# In views or services
def approve_order(order_id, user):
    try:
        order = Order.objects.get(pk=order_id)
    except Order.DoesNotExist:
        raise NotFound(f"Order {order_id} not found.")

    if order.user != user:
        raise PermissionDenied("You cannot approve this order.")

    if order.status != "pending":
        raise ValidationError({"status": "Only pending orders can be approved."})

    order.status = "approved"
    order.save(update_fields=["status"])
    return order
```

### Custom Exception Handler (optional)

```python
# config/exception_handler.py
from rest_framework.views import exception_handler

def custom_exception_handler(exc, context):
    response = exception_handler(exc, context)
    if response is not None:
        response.data["status_code"] = response.status_code
    return response

# settings/base.py
REST_FRAMEWORK = {
    "EXCEPTION_HANDLER": "config.exception_handler.custom_exception_handler",
}
```

---

## 7. Database Transactions

### Use atomic() for Multi-Step Operations

```python
from django.db import transaction

# As decorator
@transaction.atomic
def transfer_funds(from_account, to_account, amount):
    from_account.balance -= amount
    from_account.save(update_fields=["balance"])
    to_account.balance += amount
    to_account.save(update_fields=["balance"])
    TransferLog.objects.create(
        from_account=from_account,
        to_account=to_account,
        amount=amount,
    )

# As context manager
def create_order_with_items(order_data, items_data):
    with transaction.atomic():
        order = Order.objects.create(**order_data)
        OrderItem.objects.bulk_create([
            OrderItem(order=order, **item) for item in items_data
        ])
    return order
```

- Always wrap multi-model writes in `transaction.atomic()`.
- Use `select_for_update()` when concurrent writes could cause race conditions.
- Don't call external APIs or send emails inside transactions — do it after commit.

```python
from django.db import transaction

def approve_order(order):
    with transaction.atomic():
        order.status = "approved"
        order.save(update_fields=["status"])

    # Send email AFTER transaction commits
    transaction.on_commit(lambda: send_approval_email.delay(order.id))
```

---

## 8. Security Best Practices

### Permission Classes

```python
from rest_framework.permissions import IsAuthenticated, BasePermission


class IsOwner(BasePermission):
    """Only allow owners of an object to access it."""
    def has_object_permission(self, request, view, obj):
        return obj.user == request.user


class OrderViewSet(ModelViewSet):
    permission_classes = [IsAuthenticated, IsOwner]
```

### Input Sanitization

- Always validate input through serializers — never trust `request.data` directly.
- Use `serializer.is_valid(raise_exception=True)` to auto-return 400 on bad input.
- Parameterize all raw SQL — never concatenate user input into queries.

```python
# GOOD
Model.objects.raw("SELECT * FROM tbl WHERE id = %s", [user_input])

# BAD — SQL injection risk
Model.objects.raw(f"SELECT * FROM tbl WHERE id = {user_input}")
```

### Sensitive Data

- Never log passwords, tokens, or PII.
- Use `write_only=True` on password fields in serializers.
- Exclude sensitive fields from API responses.

---

## 9. Performance Best Practices

### Database

- Use `select_related()` and `prefetch_related()` — always.
- Use `update_fields` in `.save()` to avoid overwriting concurrent changes.
- Use `bulk_create()` / `bulk_update()` for batch operations.
- Add database indexes on frequently queried fields.
- Use `Subquery` / `OuterRef` instead of Python-side filtering for large datasets.

### Caching

```python
from django.core.cache import cache

def get_dashboard_stats():
    cache_key = "dashboard:stats"
    stats = cache.get(cache_key)
    if stats is None:
        stats = compute_expensive_stats()
        cache.set(cache_key, stats, timeout=300)  # 5 minutes
    return stats
```

### Async Tasks

- Offload heavy operations (email, PDF generation, external API calls) to **Celery tasks**.
- Never block API responses with slow operations.

---

## 10. Logging

```python
import logging

logger = logging.getLogger(__name__)


class OrderService:
    @staticmethod
    def create_order(user, data):
        logger.info("Creating order for user=%s", user.id)
        try:
            order = Order.objects.create(user=user, **data)
            logger.info("Order created: id=%s", order.id)
            return order
        except Exception:
            logger.exception("Failed to create order for user=%s", user.id)
            raise
```

- Use `logger = logging.getLogger(__name__)` per module.
- Use `logger.exception()` to auto-capture stack traces.
- Log at appropriate levels: `DEBUG` for dev, `INFO` for operations, `WARNING` for issues, `ERROR` for failures.
- Never use `print()` for logging in production code.

---

## 11. API Versioning (when needed)

```python
# settings/base.py
REST_FRAMEWORK = {
    "DEFAULT_VERSIONING_CLASS": "rest_framework.versioning.URLPathVersioning",
    "ALLOWED_VERSIONS": ["v1", "v2"],
    "DEFAULT_VERSION": "v1",
}

# config/urls.py
path("api/v1/", include("config.api_router_v1")),
path("api/v2/", include("config.api_router_v2")),
```

- Start with unversioned API. Add versioning only when breaking changes are needed.
- Prefer URL-based versioning (`/api/v1/`) over header-based.

---

## 12. Checklist — Before Shipping an Endpoint

- [ ] Serializer uses explicit `fields` (not `__all__`)
- [ ] ViewSet has proper `get_queryset()` with `select_related` / `prefetch_related`
- [ ] Proper permission classes assigned
- [ ] Input validation via serializer (field-level + cross-field if needed)
- [ ] Multi-model writes wrapped in `transaction.atomic()`
- [ ] Error responses use DRF exceptions (not raw `Response(status=400)`)
- [ ] Swagger docs have `tags`, `summary`, and schema annotations
- [ ] Tests cover: happy path, validation errors, permission denied, not found
- [ ] No N+1 queries (verify with `django-debug-toolbar` or `assertNumQueries`)
- [ ] Sensitive data excluded from responses

---
> Source: [zulfafalah/esmart-backend](https://github.com/zulfafalah/esmart-backend) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
