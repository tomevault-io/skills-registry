---
name: local-alternatives
description: Reference guide for local development alternatives to cloud services. Used by architects to plan adapter patterns enabling local execution and E2E testing without cloud deployment. Use when this capability is needed.
metadata:
  author: wtah
---

# Local Alternatives for Cloud Services

This skill provides guidance on selecting local alternatives for cloud services, enabling:
- Local development without cloud dependencies
- E2E testing without deployment
- Consistent interfaces via adapter patterns

---

## Core Principle: Repository Pattern with Adapters

All cloud service integrations MUST use the **Repository/Adapter Pattern**:

```
┌─────────────────┐
│  Business Logic │
└────────┬────────┘
         │ uses
         ▼
┌─────────────────┐
│    Interface    │  ← Defined in specs
└────────┬────────┘
         │ implemented by
    ┌────┴────┐
    ▼         ▼
┌───────┐ ┌───────┐
│ Cloud │ │ Local │
│Adapter│ │Adapter│
└───────┘ └───────┘
```

**Key Requirements:**
1. Define interface contracts in `.specs/interfaces/`
2. Cloud adapter for production
3. Local adapter for `LOCAL_RUN=true` mode
4. Adapter selection via environment variable or configuration

---

## Service Categories & Local Alternatives

### 1. Message Queues

| Cloud Service | Local Alternative | Docker Image | Notes |
|---------------|-------------------|--------------|-------|
| AWS SQS | RabbitMQ | `rabbitmq:3-management` | Use AMQP protocol |
| Azure Service Bus | RabbitMQ | `rabbitmq:3-management` | Or use Azurite (limited) |
| Google Cloud Pub/Sub | RabbitMQ | `rabbitmq:3-management` | Or use emulator |
| AWS SNS + SQS | RabbitMQ + Exchange | `rabbitmq:3-management` | Fan-out via exchanges |

**Interface Pattern:**
```
MessageQueue:
  - publish(topic: string, message: Message): Promise<void>
  - subscribe(topic: string, handler: MessageHandler): Subscription
  - acknowledge(messageId: string): Promise<void>
```

**Adapter Selection:**
- `LOCAL_RUN=true` → RabbitMQ adapter (localhost:5672)
- `LOCAL_RUN=false` → Cloud adapter (SQS/Service Bus/Pub/Sub)

---

### 2. Object/Blob Storage

| Cloud Service | Local Alternative | Docker Image | Notes |
|---------------|-------------------|--------------|-------|
| AWS S3 | MinIO | `minio/minio` | S3-compatible API |
| Azure Blob Storage | Azurite | `mcr.microsoft.com/azure-storage/azurite` | Full emulation |
| Google Cloud Storage | MinIO | `minio/minio` | Or fake-gcs-server |

**Interface Pattern:**
```
ObjectStorage:
  - upload(bucket: string, key: string, data: Buffer): Promise<string>
  - download(bucket: string, key: string): Promise<Buffer>
  - delete(bucket: string, key: string): Promise<void>
  - getSignedUrl(bucket: string, key: string, expiry: number): Promise<string>
  - list(bucket: string, prefix?: string): Promise<ObjectMetadata[]>
```

**Adapter Selection:**
- `LOCAL_RUN=true` → MinIO adapter (localhost:9000)
- `LOCAL_RUN=false` → Cloud adapter (S3/Blob/GCS)

---

### 3. Relational Databases

| Cloud Service | Local Alternative | Docker Image | Notes |
|---------------|-------------------|--------------|-------|
| AWS RDS PostgreSQL | PostgreSQL | `postgres:15` | Direct compatibility |
| Azure Database PostgreSQL | PostgreSQL | `postgres:15` | Direct compatibility |
| AWS RDS MySQL | MySQL | `mysql:8` | Direct compatibility |
| Azure SQL | SQL Server | `mcr.microsoft.com/mssql/server` | Or PostgreSQL |
| Google Cloud SQL | PostgreSQL/MySQL | `postgres:15` | Direct compatibility |

**Interface Pattern:**
```
Database:
  - query(sql: string, params: any[]): Promise<Result>
  - transaction(fn: (tx: Transaction) => Promise<T>): Promise<T>
  - healthCheck(): Promise<boolean>
```

**Note:** Use same database engine locally - no adapter needed if using standard SQL. Use environment variables for connection strings.

---

### 4. NoSQL Databases

| Cloud Service | Local Alternative | Docker Image | Notes |
|---------------|-------------------|--------------|-------|
| AWS DynamoDB | DynamoDB Local | `amazon/dynamodb-local` | Official emulator |
| Azure Cosmos DB | Cosmos DB Emulator | Windows only, or use MongoDB | Limited Linux support |
| MongoDB Atlas | MongoDB | `mongo:6` | Direct compatibility |
| Google Firestore | Firestore Emulator | `gcr.io/google.com/cloudsdktool/cloud-sdk` | Via gcloud |
| Redis (ElastiCache) | Redis | `redis:7` | Direct compatibility |

**Interface Pattern:**
```
DocumentStore:
  - get(collection: string, id: string): Promise<Document | null>
  - put(collection: string, id: string, document: Document): Promise<void>
  - delete(collection: string, id: string): Promise<void>
  - query(collection: string, filter: Filter): Promise<Document[]>
```

---

### 5. Notification Services

| Cloud Service | Local Alternative | Docker Image | Notes |
|---------------|-------------------|--------------|-------|
| AWS SNS | RabbitMQ (fanout) | `rabbitmq:3-management` | Exchange pattern |
| Azure Event Grid | RabbitMQ | `rabbitmq:3-management` | Topic exchanges |
| SendGrid/SES (Email) | MailHog | `mailhog/mailhog` | Email capture |
| Twilio (SMS) | Mock service | Custom | Log to console/file |

**Interface Pattern:**
```
NotificationService:
  - sendEmail(to: string[], subject: string, body: string): Promise<void>
  - sendSms(to: string, message: string): Promise<void>
  - publishEvent(topic: string, event: Event): Promise<void>
```

**Adapter Selection:**
- `LOCAL_RUN=true` → MailHog (email), Console logger (SMS), RabbitMQ (events)
- `LOCAL_RUN=false` → Cloud services

---

### 6. Authentication/Identity

| Cloud Service | Local Alternative | Docker Image | Notes |
|---------------|-------------------|--------------|-------|
| AWS Cognito | Keycloak | `quay.io/keycloak/keycloak` | Full OIDC support |
| Azure AD B2C | Keycloak | `quay.io/keycloak/keycloak` | Or mock JWT |
| Auth0 | Keycloak | `quay.io/keycloak/keycloak` | OIDC compatible |
| Firebase Auth | Keycloak | `quay.io/keycloak/keycloak` | Or emulator |

**Interface Pattern:**
```
AuthService:
  - validateToken(token: string): Promise<TokenPayload>
  - getUserInfo(userId: string): Promise<UserInfo>
  - hasPermission(userId: string, permission: string): Promise<boolean>
```

**Local Simplification:**
For E2E testing, `LOCAL_RUN=true` can use a simplified auth that:
- Accepts predefined test tokens
- Returns mock user data
- Bypasses real authentication (see `local-setup` skill)

---

### 7. Secrets Management

| Cloud Service | Local Alternative | Docker Image | Notes |
|---------------|-------------------|--------------|-------|
| AWS Secrets Manager | Local .env | N/A | Or HashiCorp Vault |
| Azure Key Vault | Local .env | N/A | Or HashiCorp Vault |
| Google Secret Manager | Local .env | N/A | Or HashiCorp Vault |
| HashiCorp Vault | Vault Dev Mode | `vault:latest` | `vault server -dev` |

**Interface Pattern:**
```
SecretsManager:
  - getSecret(name: string): Promise<string>
  - setSecret(name: string, value: string): Promise<void>
```

**Adapter Selection:**
- `LOCAL_RUN=true` → Environment variables or .env file
- `LOCAL_RUN=false` → Cloud secrets manager

---

### 8. Serverless Functions / Event Triggers

| Cloud Service | Local Alternative | Notes |
|---------------|-------------------|-------|
| AWS Lambda | Local HTTP server | Express/FastAPI endpoint |
| Azure Functions | Local HTTP server | Or `func start` |
| Google Cloud Functions | Local HTTP server | Or Functions Framework |

**Adapter Pattern:**
- Extract business logic into testable modules
- Thin handler wrapper for cloud-specific triggers
- Local: HTTP endpoint calling same business logic
- Cloud: Lambda/Function handler calling same business logic

---

### 9. Search Services

| Cloud Service | Local Alternative | Docker Image | Notes |
|---------------|-------------------|--------------|-------|
| AWS OpenSearch | OpenSearch | `opensearchproject/opensearch:2` | Direct compatibility |
| Azure Cognitive Search | OpenSearch | `opensearchproject/opensearch:2` | Similar API |
| Elasticsearch Service | Elasticsearch | `elasticsearch:8` | Direct compatibility |
| Algolia | MeiliSearch | `getmeili/meilisearch` | Similar features |

**Interface Pattern:**
```
SearchService:
  - index(collection: string, id: string, document: Document): Promise<void>
  - search(collection: string, query: SearchQuery): Promise<SearchResult>
  - delete(collection: string, id: string): Promise<void>
```

---

### 10. Caching

| Cloud Service | Local Alternative | Docker Image | Notes |
|---------------|-------------------|--------------|-------|
| AWS ElastiCache (Redis) | Redis | `redis:7` | Direct compatibility |
| Azure Cache for Redis | Redis | `redis:7` | Direct compatibility |
| Memorystore | Redis | `redis:7` | Direct compatibility |

**Interface Pattern:**
```
CacheService:
  - get(key: string): Promise<T | null>
  - set(key: string, value: T, ttl?: number): Promise<void>
  - delete(key: string): Promise<void>
  - exists(key: string): Promise<boolean>
```

---

## Architecture Specification Template

When architects specify services, they MUST include adapter requirements:

```markdown
## External Service: {Service Name}

### Purpose
{What this service does}

### Cloud Implementation
| Aspect | Value |
|--------|-------|
| **Provider** | {AWS/Azure/GCP} |
| **Service** | {Service name} |
| **Configuration** | {Key config} |

### Local Implementation
| Aspect | Value |
|--------|-------|
| **Alternative** | {Local service} |
| **Docker Image** | {image:tag} |
| **Port** | {port} |
| **Configuration** | {Key config} |

### Interface Contract
See: [{interface-name}](../interfaces/{interface-name}.md)

### Adapter Selection
| Environment | Adapter | Connection |
|-------------|---------|------------|
| Production | {CloudAdapter} | {connection string} |
| Local (LOCAL_RUN=true) | {LocalAdapter} | localhost:{port} |

### Data Consistency
- Schema: {Same/Compatible/Mapped}
- Message Format: {JSON/Protobuf/etc}
- Serialization: {Same across adapters}
```

---

## Docker Compose Template for Local Services

Architects should reference this in `.specs/deployment/local-services.md`:

```yaml
version: '3.8'

services:
  # Message Queue
  rabbitmq:
    image: rabbitmq:3-management
    ports:
      - "5672:5672"   # AMQP
      - "15672:15672" # Management UI
    environment:
      RABBITMQ_DEFAULT_USER: local
      RABBITMQ_DEFAULT_PASS: local

  # Object Storage
  minio:
    image: minio/minio
    ports:
      - "9000:9000"
      - "9001:9001"
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin

  # Database
  postgres:
    image: postgres:15
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: local
      POSTGRES_PASSWORD: local
      POSTGRES_DB: app

  # Cache
  redis:
    image: redis:7
    ports:
      - "6379:6379"

  # Email Testing
  mailhog:
    image: mailhog/mailhog
    ports:
      - "1025:1025"  # SMTP
      - "8025:8025"  # Web UI

  # Identity (if needed)
  keycloak:
    image: quay.io/keycloak/keycloak
    ports:
      - "8080:8080"
    environment:
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
    command: start-dev
```

---

## Checklist for Architects

When specifying external service integrations:

- [ ] Interface contract defined in `.specs/interfaces/`
- [ ] Cloud service documented with configuration
- [ ] Local alternative identified from this guide
- [ ] Docker image and port specified
- [ ] Adapter selection logic documented (LOCAL_RUN flag)
- [ ] Data format consistency ensured (same schemas)
- [ ] Connection string patterns documented for both environments
- [ ] Added to `.specs/deployment/local-services.md`

---

## Integration with Local Setup

The `local-setup-configurator` agent will:
1. Read adapter specifications from container `.specs/`
2. Generate docker-compose.yml for local services
3. Configure containers to use local adapters when `LOCAL_RUN=true`
4. Enable E2E testing across all containers locally

**Environment Variable:**
```
LOCAL_RUN=true   # Use local adapters
LOCAL_RUN=false  # Use cloud adapters (default)
```

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **Direct cloud SDK usage** | Can't test locally | Always use interface + adapter |
| **Cloud-specific types in business logic** | Tight coupling | Map to domain types at adapter boundary |
| **Different schemas per adapter** | Inconsistent behavior | Single interface, adapters handle translation |
| **Hardcoded connections** | No flexibility | Environment-based configuration |
| **Missing local alternative** | Can't run E2E locally | Every cloud service needs local option |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
