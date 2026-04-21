---
name: fjall-guide
description: Reference guide for using fjall storage engine in ECSdb Use when this capability is needed.
metadata:
  author: cameron5906
---

# fjall Storage Engine Guide

fjall is the embedded LSM-tree storage engine that powers ECSdb. This guide covers its API and best practices.

## fjall Overview

fjall is a pure Rust LSM-tree (Log-Structured Merge-tree) storage engine. It provides:
- Write-Ahead Log (WAL) for durability
- MemTable (skip list) for in-memory buffering
- SSTables (Sorted String Tables) for on-disk storage
- Background compaction
- Snapshots for consistent reads
- Partitions (our "column families")

## Core Concepts

### Keyspace
The top-level container. One keyspace per database.
```rust
use fjall::{Config, Keyspace};

let keyspace = Config::new(&data_dir)
    .max_write_buffer_size(64 * 1024 * 1024)  // 64MB memtable
    .open()?;
```

### Partition
Like a column family - a separate key-value namespace.
```rust
let partition = keyspace.open_partition("component_Position", Default::default())?;
```

### Basic Operations
```rust
// Write
partition.insert(key, value)?;

// Read
let value = partition.get(key)?; // Returns Option<Arc<[u8]>>

// Delete
partition.remove(key)?;

// Persist (sync to disk)
keyspace.persist()?;
```

### Range Scans
```rust
// Prefix scan
for result in partition.prefix(b"entity_") {
    let (key, value) = result?;
    // process
}

// Range scan
use std::ops::Bound;
for result in partition.range(start_key..end_key) {
    let (key, value) = result?;
}
```

### Snapshots
For consistent reads (MVCC):
```rust
let snapshot = keyspace.snapshot();
let value = partition.get_with_snapshot(key, &snapshot)?;
```

## ECSdb Usage Patterns

### Column Family Naming
```rust
const METADATA_CF: &str = "_metadata";
const EDGES_CF: &str = "_edges";
const SCHEMAS_CF: &str = "_schemas";

fn component_cf(name: &str) -> String {
    format!("component_{}", name)
}
```

### Key Encoding
Keys must be lexicographically sortable:
```rust
// Entity key: [uuid:16]
fn encode_entity_key(id: Uuid) -> [u8; 16] {
    *id.as_bytes()
}

// Component key: [entity_id:16][version:8]
fn encode_component_key(entity_id: Uuid, version: u64) -> [u8; 24] {
    let mut key = [0u8; 24];
    key[0..16].copy_from_slice(entity_id.as_bytes());
    key[16..24].copy_from_slice(&version.to_be_bytes());
    key
}
```

### Batch Writes
For atomicity:
```rust
// fjall doesn't have explicit batches like RocksDB
// Use persist() after a group of writes
partition1.insert(key1, value1)?;
partition2.insert(key2, value2)?;
keyspace.persist()?;  // Atomic durability point
```

### Error Handling
```rust
use fjall::Result as FjallResult;

fn storage_op() -> Result<(), StorageError> {
    partition.insert(key, value)
        .map_err(|e| StorageError::Backend(e.to_string()))?;
    Ok(())
}
```

## Performance Tuning

### MemTable Size
Larger = fewer flushes, more memory:
```rust
Config::new(&path)
    .max_write_buffer_size(128 * 1024 * 1024)  // 128MB for write-heavy
```

### Block Cache
For read-heavy workloads:
```rust
Config::new(&path)
    .block_cache_capacity(512 * 1024 * 1024)  // 512MB cache
```

### Compression
fjall handles compression internally. Configure via partition config if available.

## Common Pitfalls

1. **Forgetting to persist**: Writes are in memory until `persist()`
2. **Key ordering**: Use big-endian for numeric keys
3. **Partition lifecycle**: Open partitions are cached, don't reopen frequently
4. **Snapshot scope**: Snapshots hold resources, drop when done

## Testing with fjall

```rust
#[cfg(test)]
mod tests {
    use tempfile::TempDir;

    fn create_test_keyspace() -> Keyspace {
        let temp = TempDir::new().unwrap();
        Config::new(temp.path()).open().unwrap()
    }

    #[test]
    fn test_basic_ops() {
        let ks = create_test_keyspace();
        let part = ks.open_partition("test", Default::default()).unwrap();

        part.insert(b"key", b"value").unwrap();
        assert_eq!(part.get(b"key").unwrap().as_deref(), Some(b"value".as_slice()));
    }
}
```

## Documentation Links

- fjall crate: https://crates.io/crates/fjall
- fjall docs: https://docs.rs/fjall

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cameron5906) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
