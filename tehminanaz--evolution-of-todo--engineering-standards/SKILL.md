---
name: cloud-native-engineering-standards
description: Industrial-grade standards for microservices, event-driven architecture, and cloud-native deployments synthesized from the Islamic Todo Project. Use when this capability is needed.
metadata:
  author: tehminanaz
---

# Cloud-Native Engineering Standards

This skill documents the high-level architectural standards and engineering principles applied to build a scalable, resilient, and distributed application.

## 1. Architectural Patterns

### Microservices at Scale
- **Decoupling**: Services (Backend/Frontend) should be independent. Use **Dapr (Distributed Application Runtime)** to abstract away infrastructure (State Management, Pub/Sub, Service Discovery).
- **Sidecar Pattern**: Offload cross-cutting concerns (mTLS, retries, tracing) to a sidecar container to keep the application code clean.

### Event-Driven Architecture (EDA)
- **Async Communication**: Primary communication between services should be asynchronous via a message broker (Kafka).
- **Pub/Sub Strategy**: Producers publish "events" (e.g., `task-updated`), and multiple consumers subscribe dynamically. This prevents tight coupling and allows for easy addition of new services (e.g., a notification service).

## 2. Infrastructure & Operations

### KRaft-based Kafka (Modern Architecture)
- **ZooKeeper-less**: Use Kafka in KRaft mode for simpler management and faster recovery.
- **Resource Partitioning**: Use `KafkaNodePool` to separate broker roles and optimize resource usage on local clusters.

### Declarative Deployment (Helm)
- **Templating**: Never use raw Kubernetes YAMLs for complex apps. Use Helm charts to manage environments (Dev/Prod) through `values.yaml`.
- **Dependency Management**: Define infrastructure dependencies (Kafka, Redis, Ingress) as part of the chart or its sub-charts.

## 3. Production Hardening

### Multi-Stage Containerization
- **Layer Optimization**: Use multi-stage Docker builds to keep production images minimal and secure.
- **Rootless Execution**: Always run containers with non-root users (UID/GID 1000) to minimize security risks.

### Stability Probes
- **Startup Grace Period**: AI-heavy or library-rich services take time to initialize. Use `initialDelaySeconds: 120` for Liveness and `60` for Readiness to prevent "restart loops" on slow nodes.
- **Dapr Connectivity**: Ensure Dapr sidecars wait for the application port to be active before accepting traffic.

## 4. Resilience & Observability

### State Management
- **Statestore Abstraction**: Apps should not connect directly to Redis/Postgres for state. Use Dapr StateStore APIs. This allows switching from Redis to In-Memory or Azure CosmosDB without changing a single line of application code.

### Security (Zero Trust)
- **mTLS by Default**: Every pod-to-pod communication must be encrypted. Dapr-Sentry ensures automatic rotation and management of identity certificates.
- **Network Policies**: Limit traffic only to allowed paths (e.g., Frontend only talks to Backend, Backend only talks to Kafka).

## 5. Development Workflow (Local-to-Cloud)

### Minikube Optimization
- **Daemon Sync**: Point the Docker CLI to Minikube (`minikube docker-env`) to avoid pushing images to external registries during rapid dev cycles.
- **Resource Constraints**: When node pressure is high, disable non-essential operator components (Topic/User operators) or scale down non-critical UI dashboard pods.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tehminanaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
