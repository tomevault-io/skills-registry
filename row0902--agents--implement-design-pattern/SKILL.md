---
name: implement-design-pattern
description: Standards and boilerplate for mandatory patterns (Singleton, Observer, Strategy). Use when this capability is needed.
metadata:
  author: row0902
---

# 🏗️ Design Pattern Implementation Skill

## Context
Standardize mandatory patterns to ensure consistency and thread-safety.

## 1. Thread-Safe Singleton
Use for `Config`, `Services`. `threading.RLock` is required.

```python
import threading
from typing import TypeVar, Generic, Optional

T = TypeVar('T')

class SingletonMeta(type):
    _instances: dict[type, object] = {}
    _lock: threading.RLock = threading.RLock()

    def __call__(cls, *args, **kwargs):
        with cls._lock:
            if cls not in cls._instances:
                cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]

class MyService(metaclass=SingletonMeta):
    pass
```

## 2. Observer (EventBus)
Decouple UI and Logic.

```python
from enum import Enum
from typing import Callable, List, Dict

class EventType(Enum):
    FILE_UPDATED = "file.updated"
    ERROR_OCCURRED = "error.occurred"

class EventBus(metaclass=SingletonMeta):
    def __init__(self):
        self._subscribers: Dict[EventType, List[Callable]] = {}

    def subscribe(self, event_type: EventType, callback: Callable):
        if event_type not in self._subscribers:
            self._subscribers[event_type] = []
        self._subscribers[event_type].append(callback)

    def publish(self, event_type: EventType, data: dict = None):
        if event_type in self._subscribers:
            for callback in self._subscribers[event_type]:
                callback(data)
```

## 3. Repository
Abstract data access.

```python
from abc import ABC, abstractmethod
from typing import List, Generic, TypeVar

T = TypeVar('T')

class IRepository(ABC, Generic[T]):
    @abstractmethod
    def get(self, id: str) -> Optional[T]:
        pass

    @abstractmethod
    def add(self, item: T) -> None:
        pass
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/row0902) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
