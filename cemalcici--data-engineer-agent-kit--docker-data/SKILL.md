---
name: docker-data
description: Docker patterns for containerized data tools and local development. Use when this capability is needed.
metadata:
  author: cemalcici
---

# Docker for Data Engineering

> **Learn to THINK in containers, not installations.**

## ⚠️ Core Principles

### Reproducibility
- Same environment everywhere
- Version-pinned images
- Declarative configuration

### Isolation
- No host conflicts
- Clean dependencies
- Easy cleanup

---

## Common Patterns

### Data Platform Stack (docker-compose)
```yaml
version: '3.8'

services:
  spark-master:
    image: bitnami/spark:3.5
    environment:
      - SPARK_MODE=master
    ports:
      - "8080:8080"
      - "7077:7077"

  spark-worker:
    image: bitnami/spark:3.5
    environment:
      - SPARK_MODE=worker
      - SPARK_MASTER_URL=spark://spark-master:7077
    depends_on:
      - spark-master

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

  kafka:
    image: bitnami/kafka:3.6
    ports:
      - "9092:9092"
    environment:
      - KAFKA_CFG_NODE_ID=1
      - KAFKA_CFG_PROCESS_ROLES=broker,controller
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@kafka:9093

  postgres:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  minio_data:
  postgres_data:
```

### Multi-Stage Build
```dockerfile
# Build stage
FROM python:3.11-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user -r requirements.txt

# Production stage
FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /root/.local /root/.local
COPY src/ ./src/
ENV PATH=/root/.local/bin:$PATH
ENTRYPOINT ["python", "-m", "src.main"]
```

### Spark Submit Container
```dockerfile
FROM bitnami/spark:3.5

# Add dependencies
COPY requirements.txt /app/
RUN pip install -r /app/requirements.txt

# Add application
COPY src/ /app/src/
COPY main.py /app/

WORKDIR /app
ENTRYPOINT ["spark-submit"]
CMD ["--master", "local[*]", "main.py"]
```

---

## Best Practices

| Practice | Reason |
|----------|--------|
| Pin versions | Reproducibility |
| Use slim images | Smaller size |
| Don't run as root | Security |
| Use volumes for data | Persistence |

---

## Anti-Patterns

| Anti-Pattern | Solution |
|--------------|----------|
| `:latest` tag | Pin specific version |
| Root user | Use non-root user |
| Secrets in image | Use env vars or secrets |

---

## Related Skills

- For K8s: `kubernetes-data`
- For IaC: `infrastructure-as-code`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cemalcici) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
