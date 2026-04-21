---
name: testing-patterns
description: Software testing patterns, strategies, and best practices. Use when writing tests, discussing test architecture, mocking, fixtures, or test organization. Triggers on mentions of testing, unit tests, integration tests, pytest, jest, mocking, fixtures, TDD, test coverage. Use when this capability is needed.
metadata:
  author: eous
---

# Testing Patterns and Best Practices

## Test Structure

### Arrange-Act-Assert (AAA)
```python
def test_user_creation():
    # Arrange
    name = "Alice"
    email = "alice@example.com"

    # Act
    user = User.create(name=name, email=email)

    # Assert
    assert user.name == name
    assert user.email == email
    assert user.id is not None
```

### Given-When-Then (BDD)
```python
def test_order_total_with_discount():
    # Given a cart with items and a discount code
    cart = Cart()
    cart.add_item(Product("Widget", 100))
    cart.apply_discount("SAVE10")

    # When calculating the total
    total = cart.calculate_total()

    # Then the discount is applied
    assert total == 90
```

## Test Types

### Unit Tests
```python
# Test single units in isolation
def test_email_validation():
    assert is_valid_email("user@example.com") is True
    assert is_valid_email("invalid") is False
    assert is_valid_email("") is False
```

### Integration Tests
```python
# Test component interactions
@pytest.mark.integration
def test_user_repository_saves_to_database(db_session):
    repo = UserRepository(db_session)
    user = User(name="Alice", email="alice@test.com")

    repo.save(user)

    saved_user = repo.find_by_email("alice@test.com")
    assert saved_user.name == "Alice"
```

### End-to-End Tests
```python
# Test complete workflows
@pytest.mark.e2e
def test_checkout_flow(browser, test_server):
    browser.goto("/products")
    browser.click("Add to Cart")
    browser.goto("/checkout")
    browser.fill("email", "test@example.com")
    browser.click("Place Order")

    assert "Order confirmed" in browser.content()
```

## Fixtures and Setup

### Pytest Fixtures
```python
@pytest.fixture
def user():
    return User(name="Test User", email="test@example.com")

@pytest.fixture
def db_session():
    session = create_test_session()
    yield session
    session.rollback()
    session.close()

@pytest.fixture(scope="module")
def test_client():
    app = create_app(testing=True)
    with app.test_client() as client:
        yield client
```

### Factory Pattern
```python
class UserFactory:
    @staticmethod
    def create(**kwargs):
        defaults = {
            "name": "Test User",
            "email": f"user{uuid4().hex[:8]}@test.com",
            "active": True,
        }
        defaults.update(kwargs)
        return User(**defaults)

def test_inactive_user():
    user = UserFactory.create(active=False)
    assert user.can_login() is False
```

## Mocking

### Mock External Dependencies
```python
from unittest.mock import Mock, patch

def test_payment_processing():
    # Mock the payment gateway
    mock_gateway = Mock()
    mock_gateway.charge.return_value = {"status": "success", "id": "txn_123"}

    processor = PaymentProcessor(gateway=mock_gateway)
    result = processor.process_payment(amount=100)

    assert result.success is True
    mock_gateway.charge.assert_called_once_with(100)
```

### Patch Module Dependencies
```python
@patch("myapp.services.requests.post")
def test_api_call(mock_post):
    mock_post.return_value.json.return_value = {"data": "value"}
    mock_post.return_value.status_code = 200

    result = fetch_data()

    assert result == {"data": "value"}
```

### Context Manager
```python
def test_time_dependent_function():
    with patch("myapp.utils.datetime") as mock_datetime:
        mock_datetime.now.return_value = datetime(2024, 1, 15, 12, 0, 0)

        result = get_greeting()

        assert result == "Good afternoon"
```

## Test Organization

### File Structure
```
tests/
├── unit/
│   ├── test_user.py
│   ├── test_order.py
│   └── test_utils.py
├── integration/
│   ├── test_user_repository.py
│   └── test_api_endpoints.py
├── e2e/
│   └── test_checkout_flow.py
├── conftest.py
└── factories.py
```

### Naming Conventions
```python
# test_<module>.py
# test_<feature>_<scenario>

def test_user_creation_with_valid_email():
    pass

def test_user_creation_fails_with_invalid_email():
    pass

def test_order_total_includes_tax():
    pass

def test_order_total_excludes_tax_for_exempt_users():
    pass
```

## Parameterized Tests

### Multiple Inputs
```python
@pytest.mark.parametrize("input,expected", [
    ("hello", "HELLO"),
    ("world", "WORLD"),
    ("", ""),
    ("MixedCase", "MIXEDCASE"),
])
def test_uppercase(input, expected):
    assert uppercase(input) == expected
```

### Edge Cases
```python
@pytest.mark.parametrize("value,expected_valid", [
    (0, True),
    (-1, False),
    (100, True),
    (101, False),
    (None, False),
])
def test_age_validation(value, expected_valid):
    assert is_valid_age(value) == expected_valid
```

## Test Doubles

### Types
```python
# Dummy - passed but never used
def test_with_dummy():
    dummy_logger = None
    service = Service(logger=dummy_logger)

# Stub - returns canned answers
stub_repo = Mock()
stub_repo.find.return_value = User(name="Test")

# Spy - records calls
spy = Mock(wraps=real_object)

# Mock - pre-programmed expectations
mock_email = Mock()
mock_email.send.return_value = True

# Fake - working implementation (simplified)
class FakeRepository:
    def __init__(self):
        self._data = {}

    def save(self, obj):
        self._data[obj.id] = obj

    def find(self, id):
        return self._data.get(id)
```

## Async Testing

### Pytest-asyncio
```python
@pytest.mark.asyncio
async def test_async_fetch():
    result = await fetch_data()
    assert result is not None

@pytest.fixture
async def async_client():
    async with AsyncClient(app, base_url="http://test") as client:
        yield client

@pytest.mark.asyncio
async def test_api_endpoint(async_client):
    response = await async_client.get("/users")
    assert response.status_code == 200
```

## Test Quality

### Coverage
```bash
pytest --cov=myapp --cov-report=html
```

### Mutation Testing
```bash
mutmut run --paths-to-mutate=myapp/
```

### Property-Based Testing
```python
from hypothesis import given, strategies as st

@given(st.lists(st.integers()))
def test_sort_preserves_length(lst):
    assert len(sorted(lst)) == len(lst)

@given(st.lists(st.integers()))
def test_sort_idempotent(lst):
    assert sorted(sorted(lst)) == sorted(lst)
```

## Anti-Patterns to Avoid

- Testing implementation details
- Tests that depend on order
- Slow unit tests (> 100ms each)
- Not testing edge cases
- Over-mocking (testing mocks, not code)
- Flaky tests (non-deterministic)
- Duplicate test setup
- Missing assertions
- Testing private methods directly
- Ignoring test failures

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eous) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
