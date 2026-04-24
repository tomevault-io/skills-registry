---
name: pythonista-debugging
description: Root cause fixing and avoiding workarounds. Use when encountering errors, bugs, or problems. Triggers on "bug", "error", "fix", "debug", "workaround", "wrapper", "hack", "broken", "not working", "failing", "issue", or when tempted to add complexity to avoid fixing the real problem. Use when this capability is needed.
metadata:
  author: gigaverse-app
---

# Root Cause Fixing - No Workarounds

## Core Philosophy

**NEVER use overengineering tricks to work around problems. Always fix the root cause with proper, simple code.**

## The Anti-Pattern: Working Around Instead of Fixing

When you encounter an error, your instinct may be to:
1. Add a wrapper class
2. Use `__getattr__` for dynamic delegation
3. Return tuples and unpack them
4. Use `# type: ignore` to suppress errors
5. Create helper classes that "adapt" interfaces

**This is WRONG.** These are all signs you're working around a problem instead of fixing it.

## Common Workarounds to Avoid

### 1. Wrapper Classes with `__getattr__`

```python
# WRONG
class TestWrapper:
    def __init__(self, obj):
        self.obj = obj

    def test_helper(self):
        pass

    def __getattr__(self, name):
        return getattr(self.obj, name)

# CORRECT - Just use helper functions
def test_helper(obj):
    pass
```

### 2. Tuple Unpacking from Fixtures

```python
# WRONG
@pytest.fixture
def my_fixture():
    obj1 = create_obj1()
    obj2 = create_obj2()
    return obj1, obj2  # What does this return?

def test_something(my_fixture):
    obj1, obj2 = my_fixture  # Unclear

# CORRECT - Separate fixtures
@pytest.fixture
def obj1():
    return create_obj1()

@pytest.fixture
def obj2():
    return create_obj2()

def test_something(obj1, obj2):
    # Clear what we're using
```

### 3. Dynamic Attribute Assignment

```python
# WRONG
@pytest.fixture
def enhanced_object():
    obj = ProductionClass()
    obj.helper = lambda: None  # Dynamic attribute - breaks type checking
    return obj

# CORRECT
@pytest.fixture
def production_object():
    return ProductionClass()

def helper_method(obj):
    pass

def test_something(production_object):
    helper_method(production_object)
```

### 4. Adapter/Wrapper Classes

```python
# WRONG
class TestAdapter:
    """Adapts production class for testing."""
    def __init__(self, production_obj):
        self.obj = production_obj

    def adapted_method(self):
        return self.obj.method().to_dict()

# CORRECT - Transform inline
def test_something(production_obj):
    result = production_obj.method()
    data = result.to_dict()
    assert data["field"] == expected
```

## Red Flags

You're working around instead of fixing if you're:

1. Creating a wrapper class "just for tests"
2. Using `__getattr__` or other magic methods
3. Adding complexity to avoid changing existing code
4. Thinking "I'll just adapt this interface..."
5. Creating an "adapter" or "wrapper" class
6. Returning tuples from fixtures
7. Using `# type: ignore` to suppress errors instead of fixing types

## Questions to Ask

- Am I adding complexity to avoid fixing the real problem?
- Is there a simpler, more direct way to do this?
- Would someone reading this code understand what's happening?
- Am I using overengineering tricks instead of straightforward code?

## The Right Approach

1. **Stop** when you realize you're working around
2. **Identify** the root cause of the problem
3. **Fix** the root cause with simple, explicit code
4. **Delete** any workarounds you created

## Real Example

### Wrong - Working Around

```python
class VideoModerationSinkWrapper:
    def __init__(self, sink, fake_consumer):
        self.sink = sink
        self.fake_consumer = fake_consumer

    async def consume_all(self):
        while True:
            message = await self.fake_consumer.getone()
            if message is None:
                break
            await self.sink.on_process_message(message)

    def __getattr__(self, name):
        return getattr(self.sink, name)  # Magic delegation
```

### Correct - Fix the Root Cause

```python
@pytest_asyncio.fixture
async def kafka_consumer(kafka_setup):
    """Separate fixture for consumer."""
    consumer = FakeAIOKafkaConsumer()
    await consumer.start()
    yield consumer
    await consumer.stop()

@pytest_asyncio.fixture
async def video_moderation_sink(real_mongodb_client):
    """Separate fixture for sink."""
    sink = VideoModerationDBSink(db_client=real_mongodb_client)
    await sink.pre_run()
    yield sink

async def consume_all_messages(sink, consumer):
    """Simple helper function - no wrapper needed."""
    while True:
        message = await consumer.getone()
        if message is None:
            break
        await sink.on_process_message(message)

# Tests request what they need explicitly
async def test_something(kafka_consumer, video_moderation_sink):
    await consume_all_messages(video_moderation_sink, kafka_consumer)
```

## General Principle

**Simple, explicit code is ALWAYS better than overengineered workarounds that hide issues.**

When faced with a problem:
- Fix the root cause
- Use simple, straightforward solutions
- Write code that's easy to understand
- Don't work around with wrappers
- Don't use magic methods
- Don't create adapters

**If you find yourself thinking "I'll just wrap this..." - STOP. Fix the real problem instead.**

## Related Skills

- For pattern discovery, see `/pythonista-patterning`
- For testing patterns, see `/pythonista-testing`
- For type safety, see `/pythonista-typing`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
