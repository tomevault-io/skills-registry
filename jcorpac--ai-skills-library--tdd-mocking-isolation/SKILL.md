---
name: tdd-mocking-isolation
description: Techniques for isolating units under test using mocks, stubs, and fakes. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Mocking & Dependency Isolation

Testing in isolation ensures that your unit tests are fast, deterministic, and fail only when the logic inside the unit is broken.

## Test Double Types
- **Dummy**: Passed around but never actually used (e.g., to fill a parameter list).
- **Stub**: Provides canned answers to calls made during the test.
- **Spy**: A stub that also records some information based on how it was called.
- **Mock**: Objects pre-programmed with expectations which form a specification of the calls they are expected to receive.
- **Fake**: Have working implementations, but usually take some shortcut (e.g., an in-memory database).

## Best Practices
1. **Don't Mock Everything**: If a class is a pure data object or a simple utility, use the real thing.
2. **Mock Only What You Own**: Avoid mocking third-party libraries; wrap them in an interface instead.
3. **Verify Behavior, Not Implementation**: Focus on what the mock should *receive*, not the internal state of the mock.

## Example (Python)
```python
from unittest.mock import MagicMock

# Create a mock for an external API service
service = MagicMock()
service.get_user_data.return_value = {"id": 1, "name": "Jane Doe"}

# Use it in your test
result = my_function(service)
assert result == "Jane Doe"
service.get_user_data.assert_called_once_with(1)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
