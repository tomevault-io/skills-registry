---
name: mongodb-indexing-optimization
description: Master MongoDB indexing and query optimization. Learn index types, explain plans, performance tuning, and query analysis. Use when optimizing slow queries, analyzing performance, or designing indexes. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# MongoDB Indexing & Optimization

Master performance optimization through proper indexing.

## Quick Start

### Create Indexes
```javascript
// Single field index
await collection.createIndex({ email: 1 });

// Compound index
await collection.createIndex({ status: 1, createdAt: -1 });

// Unique index
await collection.createIndex({ email: 1 }, { unique: true });

// Sparse index (skip null values)
await collection.createIndex({ phone: 1 }, { sparse: true });

// Text index (full-text search)
await collection.createIndex({ title: 'text', description: 'text' });

// TTL index (auto-delete documents)
await collection.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 });
```

### List and Analyze Indexes
```javascript
// List all indexes
const indexes = await collection.indexes();
console.log(indexes);

// Drop an index
await collection.dropIndex('email_1');

// Drop all non-_id indexes
await collection.dropIndexes();
```

### Explain Query Plan
```javascript
// Analyze query execution
const explain = await collection.find({ email: 'test@example.com' }).explain('executionStats');

console.log(explain.executionStats);
// Shows: executionStages, nReturned, totalDocsExamined, executionTimeMillis
```

## Index Types

### Single Field Index
```javascript
// Index on one field
db.collection.createIndex({ age: 1 })

// Query uses index if searching on age
db.collection.find({ age: { $gte: 18 } })
```

### Compound Index
```javascript
// Index on multiple fields - order matters!
db.collection.createIndex({ status: 1, createdAt: -1 })

// Queries that benefit:
// 1. { status: 'active', createdAt: { $gt: date } }
// 2. { status: 'active' }
// But NOT: { createdAt: { $gt: date } } alone
```

### Array Index (Multikey)
```javascript
// Automatically created for arrays
db.collection.createIndex({ tags: 1 })

// Matches documents where tags contains value
db.collection.find({ tags: 'mongodb' })
```

### Text Index
```javascript
// Full-text search
db.collection.createIndex({ title: 'text', body: 'text' })

// Query
db.collection.find({ $text: { $search: 'mongodb database' } })
```

### Geospatial Index
```javascript
// 2D spherical for lat/long
db.collection.createIndex({ location: '2dsphere' })

// Find nearby
db.collection.find({
  location: {
    $near: { type: 'Point', coordinates: [-73.97, 40.77] },
    $maxDistance: 5000
  }
})
```

## Index Design: ESR Rule

**E**quality, **S**ort, **R**ange

```javascript
// Query: find active users, sort by created date, limit age
db.users.find({
  status: 'active',
  age: { $gte: 18 }
}).sort({ createdAt: -1 })

// Optimal index:
db.users.createIndex({
  status: 1,      // Equality
  createdAt: -1,  // Sort
  age: 1          // Range
})
```

## Performance Analysis

### Check if Query Uses Index
```javascript
const explain = await collection.find({ email: 'test@example.com' }).explain('executionStats');

// IXSCAN = Good (index scan)
// COLLSCAN = Bad (collection scan)
console.log(explain.executionStats.executionStages.stage);
```

### Covering Query
```javascript
// Query results entirely from index
db.users.createIndex({ email: 1, name: 1, _id: 1 })

// This query is "covered" - no need to fetch documents
db.users.find({ email: 'test@example.com' }, { email: 1, name: 1, _id: 0 })
```

## Python Examples

```python
from pymongo import ASCENDING, DESCENDING

# Create index
collection.create_index([('email', ASCENDING)], unique=True)

# Compound index
collection.create_index([('status', ASCENDING), ('createdAt', DESCENDING)])

# Explain plan
explain = collection.find({'email': 'test@example.com'}).explain()
print(explain['executionStats'])

# Drop index
collection.drop_index('email_1')
```

## Best Practices

✅ Index fields used in $match (early in pipeline)
✅ Use ESR rule for compound indexes
✅ Monitor index size and memory
✅ Remove unused indexes
✅ Use explain() to verify index usage
✅ Index strings with high cardinality
✅ Avoid indexing fields with many nulls
✅ Consider index intersection
✅ Regular index maintenance
✅ Monitor query performance

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
