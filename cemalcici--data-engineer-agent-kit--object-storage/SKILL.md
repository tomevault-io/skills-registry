---
name: object-storage
description: Object storage patterns for MinIO, S3, and RustFS in data platforms. Use when this capability is needed.
metadata:
  author: cemalcici
---

# Object Storage Patterns

> **Learn to THINK in objects and prefixes, not directories.**

## ⚠️ Core Principles

### Flat Namespace
- No real directories
- Prefixes simulate hierarchy
- Listing is expensive

### Eventual Consistency (sometimes)
- S3: Strong consistency since 2020
- MinIO: Strong consistency
- Design for it anyway

---

## Common Patterns

### MinIO Setup (Docker)
```yaml
# docker-compose.yml
services:
  minio:
    image: minio/minio
    command: server /data --console-address ":9001"
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      MINIO_ROOT_USER: admin
      MINIO_ROOT_PASSWORD: password
    volumes:
      - minio_data:/data
```

### Spark Configuration
```python
spark = SparkSession.builder \
    .config("spark.hadoop.fs.s3a.endpoint", "http://minio:9000") \
    .config("spark.hadoop.fs.s3a.access.key", "admin") \
    .config("spark.hadoop.fs.s3a.secret.key", "password") \
    .config("spark.hadoop.fs.s3a.path.style.access", "true") \
    .config("spark.hadoop.fs.s3a.impl", "org.apache.hadoop.fs.s3a.S3AFileSystem") \
    .getOrCreate()

df = spark.read.parquet("s3a://lakehouse/data/")
```

### Path Conventions
```
s3://lakehouse/
├── raw/                    # Bronze layer
│   ├── source_a/
│   │   └── year=2024/month=01/
│   └── source_b/
├── processed/              # Silver layer
│   └── cleaned_events/
├── curated/                # Gold layer
│   └── daily_metrics/
└── checkpoints/            # Streaming checkpoints
```

### Lifecycle Policies
```python
# MinIO client
from minio import Minio

client = Minio("minio:9000", access_key="admin", secret_key="password", secure=False)

# Set lifecycle to expire objects after 30 days
config = {
    "Rules": [{
        "ID": "expire-old",
        "Status": "Enabled",
        "Expiration": {"Days": 30},
        "Filter": {"Prefix": "raw/"}
    }]
}
client.set_bucket_lifecycle("lakehouse", config)
```

---

## Anti-Patterns

| Anti-Pattern | Solution |
|--------------|----------|
| Too many small files | Compact with Spark/Iceberg |
| Expensive listings | Use table format metadata |
| No lifecycle | Set expiration policies |

---

## Related Skills

- For Iceberg: `iceberg-patterns`
- For Spark: `spark-patterns`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cemalcici) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
