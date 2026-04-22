---
name: flink-patterns
description: Apache Flink development patterns for stream processing, DataStream API, Table API/SQL, and stateful processing. Use when this capability is needed.
metadata:
  author: cemalcici
---

# Apache Flink Patterns

> **Learn to THINK in streams, not batches processed quickly.**

## 🎯 Selective Reading Rule

| File | Description | When to Read |
|------|-------------|--------------|
| `datastream.md` | DataStream API, operators | Java/Scala development |
| `table-api.md` | Table API and Flink SQL | SQL-based processing |
| `windowing.md` | Window types, triggers, watermarks | Aggregations |
| `state-management.md` | State backends, checkpointing | Stateful processing |
| `connectors.md` | Kafka, Iceberg, JDBC connectors | Data integration |

---

## ⚠️ Core Principles

### Event Time is Truth
- Use event timestamps, not processing time
- Watermarks define completeness
- Late data requires explicit handling

### State is Your Friend
- Keyed state enables per-key processing
- Checkpoints ensure exactly-once
- RocksDB for large state

### Watermarks Control Latency vs Completeness
- Aggressive watermarks → more late data
- Conservative watermarks → higher latency
- Balance based on use case

---

## Common Patterns

### Flink SQL (Preferred for Most Cases)
```sql
-- Create Kafka source with watermarks
CREATE TABLE kafka_events (
    event_id STRING,
    user_id STRING,
    event_time TIMESTAMP(3),
    event_type STRING,
    WATERMARK FOR event_time AS event_time - INTERVAL '5' SECOND
) WITH (
    'connector' = 'kafka',
    'topic' = 'events',
    'properties.bootstrap.servers' = 'kafka:9092',
    'properties.group.id' = 'flink-consumer',
    'format' = 'json'
);

-- Create Iceberg sink
CREATE TABLE iceberg_events (
    event_id STRING,
    user_id STRING,
    event_time TIMESTAMP(3),
    event_type STRING,
    event_date DATE
) PARTITIONED BY (event_date) WITH (
    'connector' = 'iceberg',
    'catalog-name' = 'iceberg',
    'catalog-type' = 'rest',
    'uri' = 'http://nessie:19120/api/v1'
);

-- Streaming ETL
INSERT INTO iceberg_events
SELECT 
    event_id,
    user_id,
    event_time,
    event_type,
    CAST(event_time AS DATE) as event_date
FROM kafka_events;
```

### Windowed Aggregation (SQL)
```sql
-- Tumbling window (every 5 minutes)
SELECT 
    user_id,
    TUMBLE_START(event_time, INTERVAL '5' MINUTE) as window_start,
    COUNT(*) as event_count,
    COUNT(DISTINCT event_type) as unique_events
FROM kafka_events
GROUP BY 
    user_id,
    TUMBLE(event_time, INTERVAL '5' MINUTE);
```

### DataStream API (Java)
```java
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

// Enable checkpointing for exactly-once
env.enableCheckpointing(60000);  // Every 60 seconds
env.getCheckpointConfig().setCheckpointingMode(CheckpointingMode.EXACTLY_ONCE);

// Kafka source
KafkaSource<Event> source = KafkaSource.<Event>builder()
    .setBootstrapServers("kafka:9092")
    .setTopics("events")
    .setGroupId("flink-consumer")
    .setDeserializer(new EventDeserializer())
    .setStartingOffsets(OffsetsInitializer.earliest())
    .build();

DataStream<Event> events = env.fromSource(
    source,
    WatermarkStrategy
        .<Event>forBoundedOutOfOrderness(Duration.ofSeconds(5))
        .withTimestampAssigner((event, timestamp) -> event.getTimestamp()),
    "Kafka Source"
);

// Process with windowing
events
    .keyBy(Event::getUserId)
    .window(TumblingEventTimeWindows.of(Time.minutes(5)))
    .allowedLateness(Time.minutes(1))
    .sideOutputLateData(lateOutputTag)
    .aggregate(new EventAggregator())
    .addSink(icebergSink);
```

---

## State Backend Selection

| Backend | State Size | Performance | Use Case |
|---------|-----------|-------------|----------|
| HashMapStateBackend | < 1GB | Fastest | Small state |
| RocksDBStateBackend | Unlimited | Good | Large state |
| EmbeddedRocksDBStateBackend | Unlimited | Better | Kubernetes |

```yaml
# Flink configuration
state.backend: rocksdb
state.checkpoints.dir: s3://bucket/checkpoints
state.backend.incremental: true
```

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Processing-time for analytics | Non-deterministic | Use event-time |
| No watermarks | Windows never fire | Configure watermarks |
| Checkpoint too frequent | High overhead | 1-5 minute interval |
| Large Kafka messages | Slow processing | Pre-process or compress |

---

## Kubernetes Deployment
```yaml
apiVersion: flink.apache.org/v1beta1
kind: FlinkDeployment
metadata:
  name: flink-streaming-job
spec:
  image: flink:1.18
  flinkVersion: v1_18
  serviceAccount: flink
  jobManager:
    resource:
      memory: "2048m"
      cpu: 1
  taskManager:
    resource:
      memory: "4096m"
      cpu: 2
    replicas: 3
  job:
    jarURI: local:///opt/flink/jobs/streaming-job.jar
    parallelism: 6
```

---

## Related Skills

- For Kafka source: `kafka-patterns`
- For Iceberg sink: `iceberg-patterns`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cemalcici) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
