---
name: implement-tests
description: Standards for Test Driven Development (TDD) and ensuring 100% coverage. Use when this capability is needed.
metadata:
  author: row0902
---

# 🧪 Implement Tests (TDD Specialist)

## Context
Code without tests is Legacy Code. We write tests **BEFORE** or **WITH** implementation, never "later".

## 1. The TDD Cycle
1.  **Red:** Write a failing test for the desired feature.
2.  **Green:** Write just enough code to pass the test.
3.  **Refactor:** Clean up the code while tests stay green.

## 2. Test File Structure
*   Path: `tests/services/test_feature.py` (Mirror source structure).
*   Framework: `pytest`.

```python
import pytest
from unittest.mock import MagicMock
from src.services.feature import MyService

def test_service_does_action():
    # Arrange
    service = MyService()
    
    # Act
    result = service.perform_action()
    
    # Assert
    assert result is True
```

## 3. Mocking Strategy
*   **External APIs:** ALWAYS Mock (Google Drive, Network).
*   **FileSystem:** Use `tmp_path` fixture from pytest.
*   **UI:** Do not test wxPython widgets in unit tests (Mock the View/EventBus).

## 4. Execution
*   Command: `uv run pytest tests/path/to/test.py -v`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/row0902) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
