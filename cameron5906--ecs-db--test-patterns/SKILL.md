---
name: test-patterns
description: Testing patterns and conventions for ECSdb development Use when this capability is needed.
metadata:
  author: cameron5906
---

# ECSdb Testing Patterns

This guide covers testing patterns for ECSdb. Tests are MANDATORY for every ticket.

## Test File Organization

```
ecsdb-<crate>/
├── src/
│   ├── lib.rs
│   ├── module.rs
│   └── module/
│       ├── mod.rs
│       └── submodule.rs
└── tests/
    ├── common/
    │   └── mod.rs         # Shared test utilities
    └── integration_test.rs
```

## Unit Test Structure

### Basic Pattern
```rust
#[cfg(test)]
mod tests {
    use super::*;

    // Test naming: test_<function>_<scenario>_<expected>

    #[test]
    fn test_component_data_new_creates_valid_instance() {
        // Arrange
        let entity_id = Uuid::new_v4();
        let data = vec![1, 2, 3];

        // Act
        let component = ComponentData::new(entity_id, "Position", data.clone(), 1, 1);

        // Assert
        assert_eq!(component.entity_id, entity_id);
        assert_eq!(component.component_name, "Position");
        assert_eq!(component.data, data);
        assert_eq!(component.version, 1);
    }

    #[test]
    fn test_component_data_encode_key_is_sortable() {
        // Arrange
        let id = Uuid::new_v4();
        let comp1 = ComponentData { entity_id: id, version: 1, ..Default::default() };
        let comp2 = ComponentData { entity_id: id, version: 2, ..Default::default() };

        // Act
        let key1 = comp1.encode_key();
        let key2 = comp2.encode_key();

        // Assert - version 1 should sort before version 2
        assert!(key1 < key2);
    }
}
```

### Testing Error Cases
```rust
#[test]
fn test_storage_get_nonexistent_returns_none() {
    let storage = create_test_storage();

    let result = storage.get("cf", b"nonexistent").unwrap();

    assert!(result.is_none());
}

#[test]
fn test_storage_invalid_cf_returns_error() {
    let storage = create_test_storage();

    let result = storage.get("invalid_cf", b"key");

    assert!(matches!(result, Err(StorageError::ColumnFamilyNotFound(_))));
}
```

## Async Test Pattern

```rust
#[tokio::test]
async fn test_async_put_get_roundtrip() {
    // Arrange
    let storage = create_test_storage().await;
    let key = b"test_key";
    let value = b"test_value";

    // Act
    storage.put("cf", key, value).await.unwrap();
    let result = storage.get("cf", key).await.unwrap();

    // Assert
    assert_eq!(result.as_deref(), Some(value.as_slice()));
}
```

## Test Fixtures

### Temporary Directory Pattern
```rust
use tempfile::TempDir;

struct TestFixture {
    _temp_dir: TempDir,  // Underscore prefix: kept alive but not used directly
    storage: FjallStorageEngine,
}

impl TestFixture {
    fn new() -> Self {
        let temp_dir = TempDir::new().unwrap();
        let config = FjallConfig {
            data_dir: temp_dir.path().to_path_buf(),
            ..FjallConfig::testing()
        };
        let storage = FjallStorageEngine::open(config).unwrap();

        Self {
            _temp_dir: temp_dir,
            storage,
        }
    }
}

#[test]
fn test_with_fixture() {
    let fixture = TestFixture::new();
    // Use fixture.storage
}
```

### Builder Pattern for Test Data
```rust
struct TestEntityBuilder {
    id: Option<Uuid>,
    owner: Option<Uuid>,
    components: Vec<(String, Vec<u8>)>,
}

impl TestEntityBuilder {
    fn new() -> Self {
        Self {
            id: None,
            owner: None,
            components: Vec::new(),
        }
    }

    fn with_id(mut self, id: Uuid) -> Self {
        self.id = Some(id);
        self
    }

    fn with_component(mut self, name: &str, data: Vec<u8>) -> Self {
        self.components.push((name.to_string(), data));
        self
    }

    fn build(self) -> Entity {
        Entity {
            id: self.id.unwrap_or_else(Uuid::new_v4),
            owner_id: self.owner,
            // ...
        }
    }
}

#[test]
fn test_entity_with_components() {
    let entity = TestEntityBuilder::new()
        .with_component("Position", vec![1, 2, 3])
        .with_component("Velocity", vec![4, 5, 6])
        .build();

    assert_eq!(entity.components.len(), 2);
}
```

## Integration Test Pattern

```rust
// tests/storage_integration.rs

mod common;

use common::TestFixture;

#[tokio::test]
async fn test_full_entity_lifecycle() {
    let fixture = TestFixture::new().await;

    // Create entity
    let entity_id = fixture.create_entity().await;

    // Add components
    fixture.add_component(entity_id, "Position", &position_data).await;
    fixture.add_component(entity_id, "Velocity", &velocity_data).await;

    // Query
    let entity = fixture.get_entity(entity_id).await.unwrap();
    assert!(entity.has_component("Position"));
    assert!(entity.has_component("Velocity"));

    // Delete
    fixture.delete_entity(entity_id).await;
    assert!(fixture.get_entity(entity_id).await.is_none());
}
```

## Property-Based Testing

```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn test_key_encoding_roundtrip(
        id_bytes in prop::array::uniform16(any::<u8>()),
        version in any::<u64>()
    ) {
        let id = Uuid::from_bytes(id_bytes);
        let key = encode_component_key(id, version);
        let (decoded_id, decoded_version) = decode_component_key(&key).unwrap();

        prop_assert_eq!(decoded_id, id);
        prop_assert_eq!(decoded_version, version);
    }
}
```

## Benchmark Tests

```rust
#[cfg(test)]
mod benches {
    use super::*;
    use test::Bencher;

    #[bench]
    fn bench_component_serialization(b: &mut Bencher) {
        let component = create_test_component();

        b.iter(|| {
            component.to_bytes()
        });
    }
}
```

## Test Coverage Checklist

For each ticket, ensure tests cover:

- [ ] **Happy path**: Normal operation works
- [ ] **Edge cases**: Empty inputs, max values, boundaries
- [ ] **Error cases**: Invalid inputs return proper errors
- [ ] **Concurrency**: Thread-safe operations (if applicable)
- [ ] **Persistence**: Data survives restart (if applicable)

## Running Tests

```bash
# Run all tests
cargo test

# Run specific test
cargo test test_component_data_new

# Run tests in specific module
cargo test storage::tests::

# Run with output
cargo test -- --nocapture

# Run ignored tests
cargo test -- --ignored

# Continue on failure
cargo test --no-fail-fast
```

## Test Naming Convention

```
test_<unit>_<scenario>_<expected_outcome>

Examples:
- test_component_data_new_creates_valid_instance
- test_storage_get_nonexistent_returns_none
- test_key_encoding_preserves_sort_order
- test_transaction_commit_persists_changes
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cameron5906) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
