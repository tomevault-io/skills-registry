---
name: django-clean-arch
description: > Use when this capability is needed.
metadata:
  author: bloom-tool
---

# django_clean_arch

Standard Django encourages "fat models" and business logic in views or model methods. Clean Architecture inverts this: Django becomes a delivery mechanism, and the core domain is framework-independent. This skill covers the patterns, file structure, and conventions for Clean Architecture in Django.

## The Layered Structure

```
myproject/
├── domain/                    # Pure Python — no Django, no DB
│   ├── models/                # Domain entities and value objects
│   │   ├── order.py
│   │   └── user.py
│   ├── repositories/          # Abstract interfaces (Protocol/ABC)
│   │   ├── order_repository.py
│   │   └── user_repository.py
│   └── exceptions.py          # Domain-specific exceptions
│
├── application/               # Use cases — orchestrates domain
│   ├── use_cases/
│   │   ├── create_order.py
│   │   ├── cancel_order.py
│   │   └── get_order_summary.py
│   └── services/              # Application services (not domain services)
│       └── email_service.py   # Abstract interface for sending emails
│
├── infrastructure/            # Concrete implementations of interfaces
│   ├── repositories/
│   │   ├── django_order_repository.py   # Uses Django ORM
│   │   └── django_user_repository.py
│   └── services/
│       └── sendgrid_email_service.py
│
├── interfaces/                # Delivery layer — Django views, DRF serializers
│   ├── api/
│   │   ├── views.py           # Thin views: parse → use case → serialize
│   │   ├── serializers.py
│   │   └── urls.py
│   └── admin/
│       └── admin.py
│
└── config/                    # Django settings, dependency injection
    ├── settings.py
    ├── urls.py
    └── container.py           # Dependency injection wiring
```

## Domain Models (Pure Python)

Domain models are plain Python dataclasses or classes — no Django inheritance, no ORM fields:

```python
# domain/models/order.py
from __future__ import annotations
from dataclasses import dataclass, field
from decimal import Decimal
from datetime import datetime
from typing import Optional
from enum import Enum

class OrderStatus(str, Enum):
    PENDING = "pending"
    CONFIRMED = "confirmed"
    SHIPPED = "shipped"
    CANCELLED = "cancelled"

@dataclass
class OrderItem:
    product_id: int
    name: str
    price: Decimal
    quantity: int

    @property
    def subtotal(self) -> Decimal:
        return self.price * self.quantity

@dataclass
class Order:
    user_id: int
    items: list[OrderItem]
    status: OrderStatus = OrderStatus.PENDING
    id: Optional[int] = None
    created_at: Optional[datetime] = None

    @property
    def total(self) -> Decimal:
        return sum(item.subtotal for item in self.items)

    def cancel(self, reason: str) -> None:
        if self.status == OrderStatus.SHIPPED:
            raise ValueError("Cannot cancel a shipped order")
        self.status = OrderStatus.CANCELLED
        self._cancellation_reason = reason
```

## Repository Interfaces (Abstract)

```python
# domain/repositories/order_repository.py
from typing import Protocol, Optional
from myproject.domain.models.order import Order

class OrderRepository(Protocol):
    def save(self, order: Order) -> Order: ...
    def find_by_id(self, order_id: int) -> Optional[Order]: ...
    def find_by_user(self, user_id: int) -> list[Order]: ...
    def delete(self, order_id: int) -> None: ...
```

## Use Cases

Each use case handles exactly one business operation. It receives dependencies via constructor, and exposes a single `execute()` method:

```python
# application/use_cases/create_order.py
from dataclasses import dataclass
from decimal import Decimal
from typing import Optional
from myproject.domain.models.order import Order, OrderItem
from myproject.domain.repositories.order_repository import OrderRepository
from myproject.domain.repositories.product_repository import ProductRepository
from myproject.domain.exceptions import ProductNotFoundError

@dataclass
class CreateOrderInput:
    user_id: int
    product_ids: list[int]
    coupon_code: Optional[str] = None

@dataclass
class CreateOrderOutput:
    order_id: int
    total: Decimal
    status: str

class CreateOrderUseCase:
    def __init__(
        self,
        order_repo: OrderRepository,
        product_repo: ProductRepository,
    ) -> None:
        self._orders = order_repo
        self._products = product_repo

    def execute(self, input: CreateOrderInput) -> CreateOrderOutput:
        products = self._products.find_by_ids(input.product_ids)
        if len(products) != len(input.product_ids):
            raise ProductNotFoundError("One or more products not found")

        items = [
            OrderItem(
                product_id=p.id,
                name=p.name,
                price=p.price,
                quantity=1,  # simplification
            )
            for p in products
        ]
        order = Order(user_id=input.user_id, items=items)
        saved = self._orders.save(order)

        return CreateOrderOutput(
            order_id=saved.id,
            total=saved.total,
            status=saved.status.value,
        )
```

## Django ORM Repository (Infrastructure)

```python
# infrastructure/repositories/django_order_repository.py
from typing import Optional
from myproject.domain.models.order import Order, OrderItem, OrderStatus
from myproject.domain.repositories.order_repository import OrderRepository
from myproject.infrastructure.orm_models import OrderORM, OrderItemORM

class DjangoOrderRepository:
    """Translates between domain Order and Django ORM OrderORM."""

    def save(self, order: Order) -> Order:
        if order.id is None:
            orm_obj = OrderORM.objects.create(
                user_id=order.user_id,
                status=order.status.value,
            )
            for item in order.items:
                OrderItemORM.objects.create(
                    order=orm_obj,
                    product_id=item.product_id,
                    name=item.name,
                    price=item.price,
                    quantity=item.quantity,
                )
        else:
            orm_obj = OrderORM.objects.get(id=order.id)
            orm_obj.status = order.status.value
            orm_obj.save()
        return self._to_domain(orm_obj)

    def find_by_id(self, order_id: int) -> Optional[Order]:
        try:
            orm_obj = OrderORM.objects.prefetch_related('items').get(id=order_id)
            return self._to_domain(orm_obj)
        except OrderORM.DoesNotExist:
            return None

    def _to_domain(self, orm_obj: OrderORM) -> Order:
        return Order(
            id=orm_obj.id,
            user_id=orm_obj.user_id,
            status=OrderStatus(orm_obj.status),
            items=[
                OrderItem(
                    product_id=item.product_id,
                    name=item.name,
                    price=item.price,
                    quantity=item.quantity,
                )
                for item in orm_obj.items.all()
            ],
        )
```

## Thin Views (Delivery Layer)

```python
# interfaces/api/views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from myproject.application.use_cases.create_order import CreateOrderUseCase, CreateOrderInput
from myproject.config.container import get_container
from .serializers import CreateOrderSerializer

class OrderCreateView(APIView):
    def post(self, request):
        # 1. Parse and validate input
        serializer = CreateOrderSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        # 2. Call use case
        use_case: CreateOrderUseCase = get_container().create_order_use_case
        output = use_case.execute(
            CreateOrderInput(
                user_id=request.user.id,
                product_ids=serializer.validated_data['product_ids'],
                coupon_code=serializer.validated_data.get('coupon_code'),
            )
        )

        # 3. Serialize output
        return Response(
            {"order_id": output.order_id, "total": str(output.total)},
            status=status.HTTP_201_CREATED,
        )
```

## Dependency Injection / Container

Wire up concrete implementations to interfaces in one place:

```python
# config/container.py
from functools import lru_cache
from myproject.infrastructure.repositories.django_order_repository import DjangoOrderRepository
from myproject.infrastructure.repositories.django_product_repository import DjangoProductRepository
from myproject.application.use_cases.create_order import CreateOrderUseCase

class Container:
    @property
    def order_repository(self):
        return DjangoOrderRepository()

    @property
    def product_repository(self):
        return DjangoProductRepository()

    @property
    def create_order_use_case(self) -> CreateOrderUseCase:
        return CreateOrderUseCase(
            order_repo=self.order_repository,
            product_repo=self.product_repository,
        )

@lru_cache(maxsize=1)
def get_container() -> Container:
    return Container()
```

## Testing Strategy

The biggest win of Clean Architecture is testability. Use cases can be tested with in-memory repositories — no DB, no Django test client, no fixtures:

```python
def test_create_order_calculates_correct_total():
    order_repo = InMemoryOrderRepository()
    product_repo = InMemoryProductRepository([
        Product(id=1, name="Book", price=Decimal("29.99")),
        Product(id=2, name="Pen", price=Decimal("4.99")),
    ])
    use_case = CreateOrderUseCase(order_repo, product_repo)

    output = use_case.execute(CreateOrderInput(user_id=1, product_ids=[1, 2]))

    assert output.total == Decimal("34.98")
    assert len(order_repo.find_by_user(user_id=1)) == 1
```

## Key Rules

- Domain models never import from Django.
- Use cases never import from Django.
- Repositories in the domain layer are abstract (Protocol); concrete ones live in infrastructure.
- Views are thin: parse → use case → serialize. No business logic in views.
- Django ORM models (for migrations and DB access) live in `infrastructure/` and are separate from domain models.

---
> Source: [bloom-tool/bloom](https://github.com/bloom-tool/bloom) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
