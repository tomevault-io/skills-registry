---
name: mongodb-app-development
description: Master MongoDB integration in applications with Node.js, Python, and Java drivers. Learn connections, transactions, error handling, and best practices. Use when building applications with MongoDB. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# MongoDB Application Development

Master MongoDB integration in production applications.

## Quick Start

### Node.js Driver Setup
```javascript
const { MongoClient } = require('mongodb');

const client = new MongoClient('mongodb://localhost:27017', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
  maxPoolSize: 10
});

async function main() {
  try {
    await client.connect();
    const db = client.db('myapp');
    const collection = db.collection('users');

    // Use collection

  } finally {
    await client.close();
  }
}

main().catch(console.error);
```

### Connection Pooling
```javascript
// Configure connection pool
const client = new MongoClient(
  'mongodb://localhost:27017',
  {
    maxPoolSize: 10,        // Maximum connections
    minPoolSize: 5,         // Minimum connections
    maxIdleTimeMS: 45000,   // Idle timeout
    serverSelectionTimeoutMS: 5000
  }
);
```

### Transactions
```javascript
const session = client.startSession();

try {
  await session.withTransaction(async () => {
    // Multiple operations atomic together
    await users.insertOne({ name: 'John' }, { session });
    await accounts.insertOne({ userId: '...', balance: 100 }, { session });

    // If any fails, all roll back
  });
} catch (error) {
  console.error('Transaction failed:', error);
} finally {
  await session.endSession();
}
```

### Error Handling
```javascript
try {
  await collection.insertOne({ email: 'test@example.com' });
} catch (error) {
  if (error.code === 11000) {
    console.log('Duplicate key error');
  } else if (error.name === 'MongoNetworkError') {
    console.log('Network error - retry');
  } else if (error.name === 'MongoWriteConcernError') {
    console.log('Write concern failed');
  } else {
    console.log('Other error:', error);
  }
}
```

### Bulk Operations
```javascript
const bulk = collection.initializeUnorderedBulkOp();

bulk.insert({ name: 'John', age: 30 });
bulk.insert({ name: 'Jane', age: 28 });
bulk.find({ age: { $lt: 25 } }).update({ $set: { status: 'young' } });
bulk.find({ name: 'old_user' }).remove();

await bulk.execute();
```

## Change Streams

```javascript
// Watch collection for changes
const changeStream = collection.watch();

changeStream.on('change', (change) => {
  console.log('Change:', change.operationType);
  console.log('Document:', change.fullDocument);
});

// With pipeline filtering
const pipeline = [
  { $match: { 'operationType': 'insert' } },
  { $match: { 'fullDocument.status': 'active' } }
];

const filteredStream = collection.watch(pipeline);
```

## Python Integration

```python
from pymongo import MongoClient
from pymongo.errors import DuplicateKeyError
import asyncio

# Synchronous
client = MongoClient('mongodb://localhost:27017')
db = client['myapp']
users = db['users']

# CRUD
user = users.insert_one({'name': 'John', 'email': 'john@example.com'})
found = users.find_one({'_id': user.inserted_id})
users.update_one({'_id': user.inserted_id}, {'$set': {'age': 30}})
users.delete_one({'_id': user.inserted_id})

# Transactions
def transfer_funds(from_user_id, to_user_id, amount):
    with client.start_session() as session:
        with session.start_transaction():
            users.update_one(
                {'_id': from_user_id},
                {'$inc': {'balance': -amount}},
                session=session
            )
            users.update_one(
                {'_id': to_user_id},
                {'$inc': {'balance': amount}},
                session=session
            )

# Error handling
try:
    users.insert_one({'email': 'test@example.com'})
except DuplicateKeyError:
    print('Email already exists')
```

## Java Integration

```java
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.MongoCollection;
import org.bson.Document;

public class MongoDB {
    public static void main(String[] args) {
        // Connect
        MongoClient mongoClient = MongoClients.create("mongodb://localhost:27017");
        MongoDatabase database = mongoClient.getDatabase("myapp");
        MongoCollection<Document> users = database.getCollection("users");

        // Insert
        Document user = new Document("name", "John")
            .append("email", "john@example.com")
            .append("age", 30);
        users.insertOne(user);

        // Find
        Document found = users.find(new Document("email", "john@example.com")).first();

        // Update
        users.updateOne(
            new Document("email", "john@example.com"),
            new Document("$set", new Document("age", 31))
        );

        // Delete
        users.deleteOne(new Document("email", "john@example.com"));

        // Close
        mongoClient.close();
    }
}
```

## Performance Patterns

```javascript
// Pagination
async function paginate(collection, page = 1, limit = 10) {
  const skip = (page - 1) * limit;
  return collection
    .find({})
    .skip(skip)
    .limit(limit)
    .toArray();
}

// Batch processing
async function processBatch(collection, batchSize = 1000) {
  const cursor = collection.find({});

  let batch = [];
  for (const doc of await cursor.toArray()) {
    batch.push(doc);

    if (batch.length === batchSize) {
      await processBatch(batch);
      batch = [];
    }
  }

  if (batch.length > 0) {
    await processBatch(batch);
  }
}

// Lazy loading
class LazyLoader {
  constructor(collection, query) {
    this.cursor = collection.find(query).batchSize(100);
  }

  async next() {
    return this.cursor.hasNext() ? this.cursor.next() : null;
  }
}
```

## Best Practices

✅ Always close connections properly
✅ Use connection pooling
✅ Handle errors appropriately
✅ Use transactions for multi-document operations
✅ Validate input data
✅ Use appropriate write concerns
✅ Monitor connection status
✅ Implement retry logic
✅ Use indexes for queries
✅ Log important operations
✅ Test error scenarios
✅ Use async/await for Node.js

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
