---
name: secrets-management
description: Use when designing secret storage, rotation, or credential management systems. Covers HashiCorp Vault patterns, AWS Secrets Manager, Azure Key Vault, secret rotation, and zero-knowledge architectures.
metadata:
  author: melodic-software
---

# Secrets Management

Comprehensive guide to managing secrets, credentials, and sensitive configuration - from storage to rotation to access control.

## When to Use This Skill

- Designing secret storage architecture
- Implementing secret rotation
- Integrating applications with secret stores
- Managing API keys, passwords, certificates
- Understanding Vault, AWS Secrets Manager, Azure Key Vault
- Zero-knowledge and envelope encryption patterns

## Secrets Management Fundamentals

### What Are Secrets?

```text
Types of Secrets:

Credentials:
├── Database passwords
├── API keys
├── OAuth client secrets
├── SSH private keys
└── Service account tokens

Certificates:
├── TLS certificates and private keys
├── Code signing certificates
├── Client certificates
└── CA certificates

Encryption Keys:
├── Data encryption keys (DEK)
├── Key encryption keys (KEK)
├── HMAC keys
└── Signing keys

Sensitive Configuration:
├── Connection strings
├── License keys
├── Webhook URLs with tokens
└── Third-party credentials
```

### Secrets Lifecycle

```text
Secret Lifecycle:

1. Generation
   └── Create with sufficient entropy
   └── Use cryptographic randomness
   └── Appropriate key length

2. Storage
   └── Encrypt at rest
   └── Access control
   └── Audit logging

3. Distribution
   └── Secure transport (TLS)
   └── Just-in-time access
   └── Minimize copies

4. Usage
   └── Memory protection
   └── Minimize exposure window
   └── Clear after use

5. Rotation
   └── Regular schedule
   └── Zero-downtime rotation
   └── Update all consumers

6. Revocation
   └── Immediate effect
   └── Propagate to all systems
   └── Audit trail

7. Destruction
   └── Secure deletion
   └── Verify removal
   └── Clear backups
```

## Architecture Patterns

### Centralized Secret Store

```text
Centralized Architecture:

┌─────────────────────────────────────────────────┐
│              Secret Store                        │
│  ┌─────────────────────────────────────────┐    │
│  │  • Encrypted storage                    │    │
│  │  • Access control                       │    │
│  │  • Audit logging                        │    │
│  │  • Rotation management                  │    │
│  │  • High availability                    │    │
│  └─────────────────────────────────────────┘    │
└─────────────────────┬───────────────────────────┘
                      │
        ┌─────────────┼─────────────┐
        │             │             │
   ┌────▼────┐  ┌────▼────┐  ┌────▼────┐
   │ App A   │  │ App B   │  │ App C   │
   │         │  │         │  │         │
   │Fetches  │  │Fetches  │  │Fetches  │
   │secrets  │  │secrets  │  │secrets  │
   │on start │  │on demand│  │cached   │
   └─────────┘  └─────────┘  └─────────┘

Benefits:
+ Single source of truth
+ Centralized audit
+ Consistent policies
+ Easier rotation

Challenges:
- Single point of failure
- Network dependency
- Latency for secret access
```

### Envelope Encryption

```text
Envelope Encryption:

┌─────────────────────────────────────────────────┐
│                 Key Hierarchy                    │
│                                                  │
│  ┌─────────────┐                                │
│  │ Master Key  │  (Never leaves KMS)            │
│  │   (KEK)     │                                │
│  └──────┬──────┘                                │
│         │ Encrypts                              │
│         ▼                                       │
│  ┌─────────────┐                                │
│  │  Data Key   │  (Wrapped/encrypted)           │
│  │   (DEK)     │                                │
│  └──────┬──────┘                                │
│         │ Encrypts                              │
│         ▼                                       │
│  ┌─────────────┐                                │
│  │    Data     │  (Your secrets/data)           │
│  │             │                                │
│  └─────────────┘                                │
└─────────────────────────────────────────────────┘

Flow:
1. Generate DEK locally
2. Encrypt data with DEK
3. Send DEK to KMS for wrapping with KEK
4. Store: encrypted data + wrapped DEK
5. To decrypt: unwrap DEK with KMS, decrypt data

Benefits:
- Master key never exposed
- Can rotate DEK without re-encrypting all data
- Distributed encryption (KMS not a bottleneck)
```

### Zero-Knowledge Architecture

```text
Zero-Knowledge Secret Access:

                                    ┌──────────────┐
                                    │    Client    │
                                    │              │
                                    │ Has: secret  │
                                    │ key derived  │
                                    │ from password│
                                    └──────┬───────┘
                                           │
                    ┌──────────────────────▼───────────────────────┐
                    │                   Server                      │
                    │                                               │
                    │  Stores: encrypted secrets                    │
                    │  Cannot decrypt (doesn't have key)            │
                    │                                               │
                    │  User's secrets = Encrypt(data, userDerivedKey)│
                    └───────────────────────────────────────────────┘

Properties:
- Server cannot read secrets even if compromised
- User password never transmitted
- Key derived client-side using KDF
- Server only sees encrypted blobs

Trade-offs:
+ Maximum privacy
+ Server breach doesn't expose secrets
- Can't recover if user forgets password
- Can't audit what's stored
- Server can't validate secrets
```

## HashiCorp Vault

### Vault Architecture

```text
Vault Components:

┌─────────────────────────────────────────────────────┐
│                     Vault                            │
│                                                      │
│  ┌──────────────────────────────────────────────┐  │
│  │                 API Layer                     │  │
│  │  (HTTP/HTTPS interface for all operations)   │  │
│  └─────────────────────┬────────────────────────┘  │
│                        │                            │
│  ┌─────────────────────▼────────────────────────┐  │
│  │              Auth Methods                     │  │
│  │  Token │ OIDC │ LDAP │ K8s │ AWS │ Azure    │  │
│  └─────────────────────┬────────────────────────┘  │
│                        │                            │
│  ┌─────────────────────▼────────────────────────┐  │
│  │             Secrets Engines                   │  │
│  │  KV │ Database │ PKI │ Transit │ AWS │ SSH   │  │
│  └─────────────────────┬────────────────────────┘  │
│                        │                            │
│  ┌─────────────────────▼────────────────────────┐  │
│  │              Storage Backend                  │  │
│  │  Consul │ Raft │ S3 │ DynamoDB │ etcd        │  │
│  └──────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

### Vault Secrets Engines

```text
Common Secrets Engines:

1. KV (Key-Value)
   - Static secrets storage
   - Versioning support
   - Simplest engine

2. Database
   - Dynamic database credentials
   - Auto-rotation
   - Short-lived credentials

   vault write database/roles/my-role \
     db_name=my-database \
     creation_statements="CREATE USER..." \
     default_ttl="1h" \
     max_ttl="24h"

3. PKI
   - Certificate authority
   - Issue short-lived certificates
   - Auto-renewal

4. Transit
   - Encryption as a service
   - Key management
   - Sign/verify operations

5. AWS/Azure/GCP
   - Dynamic cloud credentials
   - IAM role assumption
   - Temporary access
```

### Vault Authentication

```text
Auth Method Selection:

Kubernetes:
- Best for: Pods in Kubernetes
- Identity: Service account token
- Config: Bound namespace/SA

vault auth enable kubernetes
vault write auth/kubernetes/config \
  kubernetes_host="https://kubernetes:443" \
  kubernetes_ca_cert=@ca.crt

AppRole:
- Best for: CI/CD, applications
- Identity: Role ID + Secret ID
- Config: Bound CIDR, metadata

vault auth enable approle
vault write auth/approle/role/my-app \
  token_ttl=1h \
  token_max_ttl=4h \
  secret_id_num_uses=1

OIDC:
- Best for: Human users
- Identity: JWT from IdP
- Config: OIDC provider connection
```

## Cloud Secret Managers

### AWS Secrets Manager

```text
AWS Secrets Manager Features:

Capabilities:
├── Automatic rotation (Lambda-based)
├── Cross-region replication
├── IAM integration
├── CloudTrail auditing
└── Resource policies

Rotation Flow:
┌─────────────────────────────────────────────────┐
│ 1. createSecret   - Create new secret version  │
│ 2. setSecret      - Update resource (RDS, etc) │
│ 3. testSecret     - Verify new secret works    │
│ 4. finishSecret   - Mark rotation complete     │
└─────────────────────────────────────────────────┘

Access Pattern:
aws secretsmanager get-secret-value \
  --secret-id my-secret \
  --version-stage AWSCURRENT

SDK Integration:
client = boto3.client('secretsmanager')
response = client.get_secret_value(SecretId='my-secret')
secret = json.loads(response['SecretString'])
```

### Azure Key Vault

```text
Azure Key Vault Features:

Object Types:
├── Secrets - Generic key/value
├── Keys - Cryptographic keys (HSM-backed)
└── Certificates - X.509 certificates

Access Control:
- RBAC (recommended)
- Access policies (legacy)
- Managed identities for Azure resources

Soft Delete + Purge Protection:
- Deleted secrets retained for recovery period
- Purge protection prevents permanent deletion
- Required for compliance scenarios

Integration:
// .NET example with DefaultAzureCredential
var client = new SecretClient(
    new Uri("https://my-vault.vault.azure.net/"),
    new DefaultAzureCredential());

KeyVaultSecret secret = await client.GetSecretAsync("my-secret");
```

### GCP Secret Manager

```text
GCP Secret Manager Features:

Capabilities:
├── Automatic replication (regional/global)
├── IAM integration
├── Audit logging
├── Secret versioning
└── Expiration support

Access Control:
- roles/secretmanager.secretAccessor
- roles/secretmanager.admin
- IAM conditions for fine-grained access

Workload Identity:
- GKE pods use service accounts
- No credential files needed
- Automatic token refresh

Access Pattern:
gcloud secrets versions access latest --secret=my-secret

# Python
from google.cloud import secretmanager
client = secretmanager.SecretManagerServiceClient()
response = client.access_secret_version(name=secret_version_name)
secret = response.payload.data.decode('UTF-8')
```

## Secret Rotation

### Zero-Downtime Rotation

```text
Dual-Version Strategy:

Phase 1: Create New Version
┌─────────────────────────────────┐
│ Secret Store                     │
│ ├── Version 1 (current) ✓       │
│ └── Version 2 (pending)  NEW    │
└─────────────────────────────────┘

Phase 2: Update Consumers
┌─────────────────────────────────┐
│ Apps accept: Version 1 OR 2     │
│ Resource updated to Version 2   │
└─────────────────────────────────┘

Phase 3: Verify New Version
┌─────────────────────────────────┐
│ Test connections with Version 2 │
│ Monitor for errors              │
└─────────────────────────────────┘

Phase 4: Deprecate Old Version
┌─────────────────────────────────┐
│ Secret Store                     │
│ ├── Version 1 (deprecated)      │
│ └── Version 2 (current) ✓       │
└─────────────────────────────────┘

Phase 5: Remove Old Version
┌─────────────────────────────────┐
│ Secret Store                     │
│ └── Version 2 (current) ✓       │
└─────────────────────────────────┘
```

### Database Credential Rotation

```text
Database Rotation Pattern:

Approach 1: Dual Users
┌─────────────────────────────────────────────────┐
│ Database has TWO users for the app:            │
│ - app_user_a (current)                          │
│ - app_user_b (standby)                          │
│                                                 │
│ Rotation:                                       │
│ 1. Generate new password for app_user_b        │
│ 2. Update secret to point to app_user_b        │
│ 3. Apps pick up new credentials                │
│ 4. Change app_user_a password (now standby)    │
└─────────────────────────────────────────────────┘

Approach 2: Dynamic Credentials (Vault)
┌─────────────────────────────────────────────────┐
│ Each credential request creates new user:       │
│ - v-app-abc123-ttl-1h (expires in 1 hour)      │
│                                                 │
│ Benefits:                                       │
│ - No rotation needed                            │
│ - Automatic cleanup                             │
│ - Per-request credentials                       │
│                                                 │
│ Vault handles:                                  │
│ - Creating users                                │
│ - Setting expiration                            │
│ - Revoking expired credentials                  │
└─────────────────────────────────────────────────┘
```

## Application Integration

### Sidecar Pattern

```text
Sidecar Secret Injection:

┌─────────────────────────────────────────────────┐
│                    Pod                           │
│  ┌─────────────────┐  ┌─────────────────────┐  │
│  │   Vault Agent   │  │      App            │  │
│  │   (Sidecar)     │  │                     │  │
│  │                 │  │                     │  │
│  │  - Authenticates│  │  Reads secrets from │  │
│  │  - Fetches      │──►  /vault/secrets/    │  │
│  │  - Renders      │  │  (shared volume)    │  │
│  │  - Refreshes    │  │                     │  │
│  └─────────────────┘  └─────────────────────┘  │
│            │                    │               │
│            ▼                    │               │
│     [Shared Volume]◄────────────┘               │
│     /vault/secrets/                             │
│     └── db-password                             │
│     └── api-key                                 │
└─────────────────────────────────────────────────┘
```

### CSI Driver Pattern

```text
Secrets Store CSI Driver:

apiVersion: v1
kind: Pod
spec:
  containers:
  - name: app
    volumeMounts:
    - name: secrets
      mountPath: "/mnt/secrets"
      readOnly: true
  volumes:
  - name: secrets
    csi:
      driver: secrets-store.csi.k8s.io
      readOnly: true
      volumeAttributes:
        secretProviderClass: "vault-database"

---
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: vault-database
spec:
  provider: vault
  parameters:
    vaultAddress: "https://vault:8200"
    roleName: "db-role"
    objects: |
      - objectName: "db-password"
        secretPath: "secret/data/db"
        secretKey: "password"
```

## Best Practices

```text
Security Best Practices:

1. Never Store Secrets In:
   ❌ Source code
   ❌ Environment variables (visible in logs)
   ❌ Container images
   ❌ Kubernetes ConfigMaps
   ❌ Plain text files

2. Access Control:
   □ Least privilege access
   □ Service-specific credentials
   □ Time-limited access where possible
   □ Regular access reviews

3. Audit and Monitoring:
   □ Log all secret access
   □ Alert on unusual patterns
   □ Regular audit reviews
   □ Compliance reporting

4. Rotation:
   □ Automate all rotations
   □ Short credential lifetimes
   □ Test rotation procedures
   □ Document emergency rotation

5. High Availability:
   □ Secret store must be HA
   □ Cache secrets for availability
   □ Graceful degradation plan
   □ Recovery procedures documented
```

## Related Skills

- `zero-trust-architecture` - Overall security architecture
- `api-security` - API authentication and authorization
- `mtls-service-mesh` - Certificate management for services
- `container-orchestration` - Kubernetes secrets integration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
