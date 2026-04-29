---
name: mongodb-schema-design
description: Master MongoDB schema design and data modeling patterns. Learn embedding vs referencing, relationships, normalization, and schema evolution. Use when designing databases, normalizing data, or optimizing queries. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# MongoDB Schema Design

Master data modeling and schema patterns.

## Quick Start

### One-to-One: Embedded
```javascript
// User with single address - embed if always accessed together
{
  _id: ObjectId('...'),
  name: 'John',
  email: 'john@example.com',
  address: {
    street: '123 Main St',
    city: 'New York',
    zip: '10001'
  }
}
```

### One-to-Many: Embed Array
```javascript
// User with multiple tags - embed if limited size
{
  _id: ObjectId('...'),
  name: 'John',
  tags: ['mongodb', 'database', 'nosql'],
  posts: [
    { _id: 1, title: 'Post 1', content: '...' },
    { _id: 2, title: 'Post 2', content: '...' }
  ]
}
```

### One-to-Many: Reference
```javascript
// User with many orders - reference if potentially large
{
  _id: ObjectId('user1'),
  name: 'John',
  email: 'john@example.com'
}

// Orders collection
{
  _id: ObjectId('order1'),
  customerId: ObjectId('user1'),
  total: 99.99
}
```

### Many-to-Many: Array of References
```javascript
// Products with categories
{
  _id: ObjectId('product1'),
  name: 'Laptop',
  categoryIds: [
    ObjectId('electronics'),
    ObjectId('computers')
  ]
}

// Categories collection
{
  _id: ObjectId('electronics'),
  name: 'Electronics'
}
```

## Schema Patterns

### Attribute Pattern
```javascript
// Store variant attributes flexibly
{
  _id: ObjectId('...'),
  productName: 'T-Shirt',
  attributes: [
    { key: 'color', value: 'blue' },
    { key: 'size', value: 'L' },
    { key: 'material', value: 'cotton' }
  ]
}
```

### Polymorphic Pattern
```javascript
// Different document types in same collection
{
  _id: ObjectId('...'),
  type: 'email',
  to: 'user@example.com',
  subject: 'Hello'
}

{
  _id: ObjectId('...'),
  type: 'sms',
  phoneNumber: '+1234567890',
  message: 'Hi there'
}
```

### Tree Structures: Adjacency List
```javascript
// Parent-child relationships
{
  _id: ObjectId('...'),
  name: 'Electronics',
  parent: null
}

{
  _id: ObjectId('...'),
  name: 'Computers',
  parent: ObjectId('electronics')
}
```

### Versioned Pattern
```javascript
// Track document history
{
  _id: ObjectId('...'),
  name: 'Product',
  description: 'Latest description',
  versions: [
    { v: 1, name: 'Product', description: 'Original', date: ISODate(...) },
    { v: 2, name: 'Product', description: 'Updated', date: ISODate(...) }
  ]
}
```

## Design Principles

### Embedding Advantages
- Single query to fetch related data
- Atomic updates for related documents
- No joins needed

### Referencing Advantages
- Avoid data duplication
- Smaller documents
- Flexible relationships
- Can grow independently

### Decision Tree
```
Does the related data grow unbounded?
  YES → Use referencing
  NO → Consider embedding

Is the related data frequently accessed separately?
  YES → Use referencing
  NO → Consider embedding

Do updates need to be atomic across documents?
  YES → Use embedding
  NO → Use referencing
```

## Python Design Example

```python
# User with embedded address
users.insert_one({
    'name': 'John',
    'email': 'john@example.com',
    'address': {
        'street': '123 Main St',
        'city': 'New York'
    }
})

# User with references to orders
users.insert_one({
    '_id': ObjectId('...'),
    'name': 'John'
})

orders.insert_one({
    'userId': ObjectId('...'),
    'total': 99.99
})

# Query with $lookup
users.aggregate([
    { '$lookup': {
        'from': 'orders',
        'localField': '_id',
        'foreignField': 'userId',
        'as': 'orders'
    }}
])
```

## Best Practices

✅ Embed when data is always accessed together
✅ Reference for unbounded arrays
✅ Keep document size under 16MB
✅ Consider query patterns when designing
✅ Denormalize carefully for performance
✅ Plan for schema evolution
✅ Use validation schemas
✅ Document your design decisions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
