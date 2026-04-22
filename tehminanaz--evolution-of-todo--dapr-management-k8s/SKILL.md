---
name: dapr-management-k8s
description: Best practices for Dapr sidecar injection, mTLS configuration, and component management in Kubernetes. Use when this capability is needed.
metadata:
  author: tehminanaz
---

# Dapr Management in Kubernetes

This skill covers the setup and stabilization of Dapr within a Kubernetes cluster (specifically Minikube/local environments).

## Core Concepts

### 1. Security (mTLS)
Dapr sidecars require a `Configuration` resource to successfully initialize mTLS. Without this, you may see `PermissionDenied` errors during sidecar startup.

**Essential `appconfig.yaml`**:
```yaml
apiVersion: dapr.io/v1alpha1
kind: Configuration
metadata:
  name: appconfig
spec:
  mtls:
    enabled: true
    workloadCertTTL: 24h
    allowedClockSkew: 15m
  tracing:
    samplingRate: "1"
    zipkin:
      endpointAddress: "http://jaeger-collector.default.svc.cluster.local:9411/api/v2/spans"
```

### 2. Sidecar Injection
Ensure deployments have the correct annotations. On resource-constrained nodes, it is critical to increase the sidecar probe delays.

**Deployment Annotations**:
```yaml
annotations:
  dapr.io/enabled: "true"
  dapr.io/app-id: "my-service"
  dapr.io/app-port: "8000"
  dapr.io/config: "appconfig"
  # Crucial for slow-starting nodes
  dapr.io/sidecar-liveness-probe-delay-seconds: "120"
  dapr.io/sidecar-readiness-probe-delay-seconds: "60"
```

### 3. Component Stabilization
- **State Store**: If Redis is unavailable, use `state.in-memory` to avoid sidecar crashes.
- **PubSub**: Use Kafka (Strimzi) for reliable distributed messaging.

## Troubleshooting
- **Sidecar CrashLoopBackOff**: Check the `daprd` container logs. If it says `failed to get configuration`, verify the `appconfig` resource exists in the namespace.
- **Connection Refused**: If the sidecar can't find the app, ensure the application is binding to `0.0.0.0` (not `127.0.0.1`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tehminanaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
