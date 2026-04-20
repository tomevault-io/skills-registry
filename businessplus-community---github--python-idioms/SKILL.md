---
name: python-idioms
description: Pythonic patterns and idioms - comprehensions, generators, context managers, decorators, dataclasses, error handling, and performance tips Use when this capability is needed.
metadata:
  author: businessplus-community
---

# Python Idioms and Patterns

Idiomatic Python patterns for writing clean, efficient, and maintainable code.

## When to Activate

- Writing new Python code
- Refactoring existing code to be more Pythonic
- Learning Python best practices
- Reviewing code for idiom compliance

## Core Principles

### 1. Readability Counts

```python
# Good: Clear and readable
def get_active_users(users: list[User]) -> list[User]:
    """Return only active users from the provided list."""
    return [user for user in users if user.is_active]

# Bad: Clever but confusing
def get_active_users(u):
    return [x for x in u if x.a]
```

### 2. Explicit is Better Than Implicit

```python
# Good: Explicit configuration
import logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

# Bad: Hidden side effects
import some_module
some_module.setup()  # What does this do?
```

### 3. EAFP - Easier to Ask Forgiveness Than Permission

```python
# Good: EAFP style
def get_value(dictionary: dict, key: str, default=None):
    try:
        return dictionary[key]
    except KeyError:
        return default

# Avoid: LBYL (Look Before You Leap)
def get_value(dictionary: dict, key: str, default=None):
    if key in dictionary:
        return dictionary[key]
    return default
```

## Type Hints

### Modern Syntax (Python 3.9+)

```python
# Use built-in types directly
def process_items(items: list[str]) -> dict[str, int]:
    return {item: len(item) for item in items}

# Union types with | (Python 3.10+)
def find_user(user_id: str) -> User | None:
    return users.get(user_id)

# Optional is Union[X, None]
from typing import Optional
def find_user(user_id: str) -> Optional[User]:
    return users.get(user_id)
```

### Type Aliases

```python
from typing import TypeVar, Any

# Type alias for complex types
JSON = dict[str, Any] | list[Any] | str | int | float | bool | None

def parse_json(data: str) -> JSON:
    return json.loads(data)

# Generic types
T = TypeVar('T')

def first(items: list[T]) -> T | None:
    """Return the first item or None if list is empty."""
    return items[0] if items else None
```

### Protocol-Based Duck Typing

```python
from typing import Protocol

class Renderable(Protocol):
    def render(self) -> str:
        """Render the object to a string."""

def render_all(items: list[Renderable]) -> str:
    """Works with any object that has a render() method."""
    return "\n".join(item.render() for item in items)
```

## Error Handling

### Specific Exception Handling

```python
# Good: Catch specific exceptions
def load_config(path: str) -> Config:
    try:
        with open(path) as f:
            return Config.from_json(f.read())
    except FileNotFoundError as e:
        raise ConfigError(f"Config file not found: {path}") from e
    except json.JSONDecodeError as e:
        raise ConfigError(f"Invalid JSON in config: {path}") from e

# Bad: Bare except
def load_config(path: str) -> Config:
    try:
        with open(path) as f:
            return Config.from_json(f.read())
    except:  # Catches everything including KeyboardInterrupt!
        return None
```

### Exception Chaining

```python
def process_data(data: str) -> Result:
    try:
        parsed = json.loads(data)
    except json.JSONDecodeError as e:
        # Chain exceptions to preserve traceback
        raise ValueError(f"Failed to parse data") from e
```

### Custom Exception Hierarchy

```python
class AppError(Exception):
    """Base exception for all application errors."""

class ValidationError(AppError):
    """Raised when input validation fails."""

class NotFoundError(AppError):
    """Raised when a requested resource is not found."""

def get_user(user_id: str) -> User:
    user = db.find_user(user_id)
    if not user:
        raise NotFoundError(f"User not found: {user_id}")
    return user
```

## Context Managers

### Resource Management

```python
# Good: Using context managers
def process_file(path: str) -> str:
    with open(path, 'r') as f:
        return f.read()

# Bad: Manual resource management
def process_file(path: str) -> str:
    f = open(path, 'r')
    try:
        return f.read()
    finally:
        f.close()
```

### Custom Context Managers

```python
from contextlib import contextmanager
import time

@contextmanager
def timer(name: str):
    """Context manager to time a block of code."""
    start = time.perf_counter()
    yield
    elapsed = time.perf_counter() - start
    print(f"{name} took {elapsed:.4f} seconds")

# Usage
with timer("data processing"):
    process_large_dataset()
```

### Context Manager Class

```python
class DatabaseTransaction:
    def __init__(self, connection):
        self.connection = connection

    def __enter__(self):
        self.connection.begin_transaction()
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is None:
            self.connection.commit()
        else:
            self.connection.rollback()
        return False  # Don't suppress exceptions

# Usage
with DatabaseTransaction(conn):
    conn.execute("INSERT ...")
    conn.execute("UPDATE ...")
```

## Comprehensions and Generators

### List Comprehensions

```python
# Good: Simple transformation
names = [user.name for user in users if user.is_active]

# Avoid: Too complex - use a function
# Bad
result = [x * 2 for x in items if x > 0 if x % 2 == 0]

# Good: Extract to function
def filter_and_double(items: list[int]) -> list[int]:
    return [x * 2 for x in items if x > 0 and x % 2 == 0]
```

### Dict Comprehensions

```python
# Create dict from list
user_by_id = {user.id: user for user in users}

# Transform dict
uppercased = {k.upper(): v for k, v in data.items()}
```

### Generator Expressions (Lazy Evaluation)

```python
# Good: Generator for large data (lazy evaluation)
total = sum(x * x for x in range(1_000_000))

# Bad: Creates large intermediate list
total = sum([x * x for x in range(1_000_000)])
```

### Generator Functions

```python
def read_large_file(path: str):
    """Read a large file line by line."""
    with open(path) as f:
        for line in f:
            yield line.strip()

# Usage - processes one line at a time
for line in read_large_file("huge.txt"):
    process(line)
```

## Data Classes

### Basic Dataclass

```python
from dataclasses import dataclass, field
from datetime import datetime

@dataclass
class User:
    """User entity with automatic __init__, __repr__, __eq__."""
    id: str
    name: str
    email: str
    created_at: datetime = field(default_factory=datetime.now)
    is_active: bool = True

# Usage
user = User(id="123", name="Alice", email="alice@example.com")
```

### Dataclass with Validation

```python
@dataclass
class User:
    email: str
    age: int

    def __post_init__(self):
        if "@" not in self.email:
            raise ValueError(f"Invalid email: {self.email}")
        if self.age < 0 or self.age > 150:
            raise ValueError(f"Invalid age: {self.age}")
```

### Frozen Dataclass (Immutable)

```python
@dataclass(frozen=True)
class Point:
    """Immutable 2D point."""
    x: float
    y: float

    def distance(self, other: 'Point') -> float:
        return ((self.x - other.x) ** 2 + (self.y - other.y) ** 2) ** 0.5

p1 = Point(0, 0)
p2 = Point(3, 4)
print(p1.distance(p2))  # 5.0
# p1.x = 10  # Raises FrozenInstanceError
```

## Decorators

### Function Decorator

```python
import functools
import time

def timer(func):
    """Decorator to time function execution."""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        elapsed = time.perf_counter() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)
```

### Parameterized Decorator

```python
def repeat(times: int):
    """Decorator to repeat a function multiple times."""
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            results = []
            for _ in range(times):
                results.append(func(*args, **kwargs))
            return results
        return wrapper
    return decorator

@repeat(times=3)
def greet(name: str) -> str:
    return f"Hello, {name}!"

# greet("Alice") returns ["Hello, Alice!", "Hello, Alice!", "Hello, Alice!"]
```

## Performance Tips

### Use __slots__ for Memory Efficiency

```python
# Regular class uses __dict__ (more memory)
class Point:
    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y

# __slots__ reduces memory usage significantly
class Point:
    __slots__ = ['x', 'y']

    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y
```

### Avoid String Concatenation in Loops

```python
# Bad: O(n²) due to string immutability
result = ""
for item in items:
    result += str(item)

# Good: O(n) using join
result = "".join(str(item) for item in items)
```

### Use Generators for Large Data

```python
# Bad: Loads all into memory
def get_all_lines(path: str) -> list[str]:
    with open(path) as f:
        return [line.strip() for line in f]

# Good: Yields one at a time
def get_all_lines(path: str):
    with open(path) as f:
        for line in f:
            yield line.strip()
```

## Anti-Patterns to Avoid

```python
# Bad: Mutable default argument
def append_to(item, items=[]):  # items is shared!
    items.append(item)
    return items

# Good: Use None
def append_to(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

# Bad: Checking type with type()
if type(obj) == list:
    process(obj)

# Good: Use isinstance
if isinstance(obj, list):
    process(obj)

# Bad: Comparing to None with ==
if value == None:
    pass

# Good: Use is
if value is None:
    pass

# Bad: from module import *
from os.path import *

# Good: Explicit imports
from os.path import join, exists
```

## Quick Reference

| Idiom | Description |
|-------|-------------|
| EAFP | Try/except over if/check |
| Context managers | `with` for resource management |
| List comprehensions | For simple transformations |
| Generators | For lazy evaluation and large data |
| Type hints | Annotate function signatures |
| Dataclasses | For data containers |
| `__slots__` | For memory optimization |
| f-strings | For string formatting |
| `pathlib.Path` | For path operations |
| `enumerate` | For index-element pairs |

**Remember**: Prioritize readability over cleverness. When in doubt, choose the obvious solution.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/businessplus-community) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
