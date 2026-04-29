---
name: mongodb-replication-sharding
description: Master MongoDB replication, replica sets, and sharding for distributed deployments. Learn failover, shard keys, and cluster management. Use when setting up high availability or scaling horizontally. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# MongoDB Replication & Sharding

Master distributed MongoDB architectures.

## Quick Start

### Replica Set Setup
```bash
# Start mongod instances with replica set config
mongod --replSet rs0 --port 27017
mongod --replSet rs0 --port 27018
mongod --replSet rs0 --port 27019

# Initiate replica set
mongo mongodb://localhost:27017
> rs.initiate({
    _id: 'rs0',
    members: [
      { _id: 0, host: 'localhost:27017', priority: 1 },
      { _id: 1, host: 'localhost:27018', priority: 0.5 },
      { _id: 2, host: 'localhost:27019', priority: 0 }
    ]
  })

# Check replica set status
> rs.status()
```

### Replication Concepts

```
Primary: Accepts reads and writes
Secondary: Replicates from primary, serves reads only
Arbiter: Participates in elections, no data
```

### Write Concerns
```javascript
// Unacknowledged
await collection.insertOne(doc, { writeConcern: { w: 0 } })

// Acknowledged (single node)
await collection.insertOne(doc, { writeConcern: { w: 1 } })

// Majority
await collection.insertOne(doc, { writeConcern: { w: 'majority' } })

// Majority with timeout
await collection.insertOne(doc, {
  writeConcern: { w: 'majority', wtimeout: 5000 }
})
```

### Read Preferences
```javascript
// Read from primary only (default)
find().setReadPreference('primary')

// Read from primary, failover to secondary
find().setReadPreference('primaryPreferred')

// Read from secondary if available
find().setReadPreference('secondary')

// Read from secondary, failover to primary
find().setReadPreference('secondaryPreferred')

// Read from nearest node
find().setReadPreference('nearest')
```

## Sharding

### Enable Sharding
```bash
# Start config servers
mongod --configsvr --dbpath /data/config0 --port 27019
mongod --configsvr --dbpath /data/config1 --port 27020
mongod --configsvr --dbpath /data/config2 --port 27021

# Start mongos router
mongos --configdb localhost:27019,localhost:27020,localhost:27021

# Start shard servers
mongod --shardsvr --dbpath /data/shard0 --port 27017
mongod --shardsvr --dbpath /data/shard1 --port 27018
```

### Shard Key Design
```javascript
// Enable sharding on database
sh.enableSharding('myapp')

// Shard collection with key
sh.shardCollection('myapp.users', { email: 1 })

// Hash-based sharding (better distribution)
sh.shardCollection('myapp.logs', { userId: 'hashed' })

// Compound shard key
sh.shardCollection('myapp.events', { tenantId: 1, timestamp: 1 })
```

### Check Sharding Status
```javascript
// Get sharding status
sh.status()

// Get shard information
db.adminCommand({ listShards: 1 })

// Get chunk distribution
sh.balancerStatus()
```

## Failover & High Availability

### Replica Set Elections
```javascript
// Trigger election (step down)
rs.stepDown()

// View election status
rs.status()

// Check oplog
db.oplog.rs.find().sort({ ts: -1 }).limit(5)
```

### Monitor Replication Lag
```javascript
// Check replica set members lag
rs.status().members

// Monitor oplog length
db.oplog.rs.find().limit(1).sort({ $natural: -1 })
```

## Transactions (MongoDB 4.0+)

```javascript
// Multi-document transaction
const session = client.startSession();
try {
  await session.withTransaction(async () => {
    await users.insertOne({ name: 'John' }, { session });
    await accounts.insertOne({ userId: '...', balance: 100 }, { session });
  });
} finally {
  await session.endSession();
}
```

## Python Examples

```python
from pymongo import MongoClient

# Connect to replica set
client = MongoClient('mongodb://localhost:27017,localhost:27018,localhost:27019/?replicaSet=rs0')

# Write concern
collection.insert_one(
    {'name': 'John'},
    write_concern=WriteConcern(w='majority')
)

# Read preference
collection.find().with_options(
    read_preference=ReadPreference.SECONDARY
)
```

## Monitoring

✅ Check replica set status regularly
✅ Monitor replication lag
✅ Watch chunk migration progress
✅ Monitor oplog size
✅ Alert on member failures
✅ Track failover events
✅ Monitor balancer activity
✅ Check data distribution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
