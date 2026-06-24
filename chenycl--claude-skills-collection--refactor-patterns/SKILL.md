---
name: refactor-patterns
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Refactoring Patterns

Common refactoring techniques and design patterns for improving code quality.

## Code Smells & Solutions

### 1. Long Functions
**Smell:** Function > 20-30 lines
**Solution:** Extract Method

```python
# Before
def process_order(order):
    # validate
    if not order.items:
        raise ValueError("Empty order")
    if not order.customer:
        raise ValueError("No customer")
    # calculate total
    subtotal = sum(item.price * item.qty for item in order.items)
    tax = subtotal * 0.1
    total = subtotal + tax
    # save
    db.save(order)
    return total

# After
def process_order(order):
    validate_order(order)
    total = calculate_total(order)
    save_order(order)
    return total

def validate_order(order):
    if not order.items:
        raise ValueError("Empty order")
    if not order.customer:
        raise ValueError("No customer")

def calculate_total(order):
    subtotal = sum(item.price * item.qty for item in order.items)
    tax = subtotal * 0.1
    return subtotal + tax
```

### 2. Duplicated Code
**Smell:** Same logic in multiple places
**Solution:** Extract and Reuse

```typescript
// Before
function getAdminUsers() {
  return users.filter(u => u.active && u.role === 'admin');
}
function getActiveUsers() {
  return users.filter(u => u.active);
}

// After
const isActive = (u: User) => u.active;
const isAdmin = (u: User) => u.role === 'admin';

const getActiveUsers = () => users.filter(isActive);
const getAdminUsers = () => users.filter(u => isActive(u) && isAdmin(u));
```

### 3. Primitive Obsession
**Smell:** Using primitives for domain concepts
**Solution:** Create Value Objects

```python
# Before
def create_user(email: str, phone: str):
    if '@' not in email:
        raise ValueError("Invalid email")
    # email used everywhere as string

# After
@dataclass
class Email:
    value: str

    def __post_init__(self):
        if '@' not in self.value:
            raise ValueError("Invalid email")

def create_user(email: Email, phone: Phone):
    # type-safe, self-validating
```

### 4. Feature Envy
**Smell:** Method uses data from another class excessively
**Solution:** Move Method

```javascript
// Before
class Order {
  getDiscountedPrice(customer) {
    if (customer.loyaltyPoints > 100) {
      return this.price * 0.9;
    }
    if (customer.memberSince < lastYear) {
      return this.price * 0.95;
    }
    return this.price;
  }
}

// After
class Customer {
  getDiscount() {
    if (this.loyaltyPoints > 100) return 0.1;
    if (this.memberSince < lastYear) return 0.05;
    return 0;
  }
}

class Order {
  getDiscountedPrice(customer) {
    return this.price * (1 - customer.getDiscount());
  }
}
```

### 5. Switch Statements
**Smell:** Long switch/if-else chains
**Solution:** Polymorphism or Strategy Pattern

```typescript
// Before
function calculatePay(employee) {
  switch (employee.type) {
    case 'hourly':
      return employee.hours * employee.rate;
    case 'salaried':
      return employee.salary / 12;
    case 'commission':
      return employee.sales * employee.commissionRate;
  }
}

// After: Strategy Pattern
interface PayStrategy {
  calculate(employee: Employee): number;
}

class HourlyPay implements PayStrategy {
  calculate(e: Employee) { return e.hours * e.rate; }
}

class SalariedPay implements PayStrategy {
  calculate(e: Employee) { return e.salary / 12; }
}

const strategies: Record<string, PayStrategy> = {
  hourly: new HourlyPay(),
  salaried: new SalariedPay(),
};

function calculatePay(employee) {
  return strategies[employee.type].calculate(employee);
}
```

## SOLID Principles

### Single Responsibility (S)
One class = one reason to change

### Open/Closed (O)
Open for extension, closed for modification

### Liskov Substitution (L)
Subtypes must be substitutable for base types

### Interface Segregation (I)
Many specific interfaces > one general interface

### Dependency Inversion (D)
Depend on abstractions, not concretions

```typescript
// Before: depends on concrete class
class OrderService {
  private db = new MySQLDatabase();
}

// After: depends on abstraction
class OrderService {
  constructor(private db: Database) {}
}
```

## Common Refactoring Operations

| Refactoring | When to Use |
|-------------|-------------|
| Extract Method | Long function, reusable logic |
| Inline Method | Method body is obvious |
| Extract Variable | Complex expression |
| Rename | Unclear naming |
| Move Method/Field | Feature envy |
| Extract Class | Class doing too much |
| Replace Conditional with Polymorphism | Complex type-based logic |
| Introduce Parameter Object | Many related parameters |
| Replace Magic Number with Constant | Unexplained literals |

## Safe Refactoring Checklist

1. [ ] Tests pass before refactoring
2. [ ] Make small, incremental changes
3. [ ] Run tests after each change
4. [ ] Commit frequently
5. [ ] Review diff before merging
6. [ ] No behavior change (unless intentional)

## References

- [Refactoring Catalog](https://refactoring.guru/refactoring/catalog)
- [Code Smells](https://refactoring.guru/refactoring/smells)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
