---
name: istio-expert
description: > Use when this capability is needed.
metadata:
  author: wbunker
---

# Istio Service Mesh Expert

## Architecture Overview

Istio follows a **control plane / data plane** architecture:

```
┌─────────────────────────────────────────────────┐
│                  Control Plane                   │
│  ┌─────────────────────────────────────────────┐│
│  │                   istiod                     ││
│  │  ┌─────────┐ ┌──────────┐ ┌──────────────┐ ││
│  │  │  Pilot   │ │  Citadel │ │    Galley    │ ││
│  │  │ (config) │ │ (certs)  │ │ (validation) │ ││
│  │  └─────────┘ └──────────┘ └──────────────┘ ││
│  └─────────────────────────────────────────────┘│
└─────────────────────────────────────────────────┘
         │ xDS API (config push)           │ CA (cert issuance)
         ▼                                 ▼
┌─────────────────────────────────────────────────┐
│                   Data Plane                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │ Pod A     │  │ Pod B     │  │ Pod C     │     │
│  │ ┌──────┐ │  │ ┌──────┐ │  │ ┌──────┐ │      │
│  │ │Envoy │ │  │ │Envoy │ │  │ │Envoy │ │      │
│  │ │proxy │ │  │ │proxy │ │  │ │proxy │ │      │
│  │ └──────┘ │  │ └──────┘ │  │ └──────┘ │      │
│  │ ┌──────┐ │  │ ┌──────┐ │  │ ┌──────┐ │      │
│  │ │ App  │ │  │ │ App  │ │  │ │ App  │ │      │
│  │ └──────┘ │  │ └──────┘ │  │ └──────┘ │      │
│  └──────────┘  └──────────┘  └──────────┘      │
└─────────────────────────────────────────────────┘
```

**istiod** — unified control plane binary combining:
- **Pilot** — converts high-level routing rules to Envoy config, pushes via xDS
- **Citadel** — CA for workload identity, issues SPIFFE certificates for mTLS
- **Galley** — config validation and processing

**Envoy proxy** — high-performance L4/L7 sidecar proxy handling all mesh traffic.

### Deployment Models

| Model | Description | Use Case |
|-------|-------------|----------|
| **Sidecar** | Envoy injected per pod | Traditional, full feature set |
| **Ambient** | Per-node ztunnel + optional waypoint proxies | Lower resource overhead, no sidecar modification |

## Key Custom Resources (CRDs)

### Traffic Management
| CRD | Purpose |
|-----|---------|
| `VirtualService` | Route rules, traffic splitting, retries, timeouts, fault injection |
| `DestinationRule` | Load balancing, connection pool, outlier detection, TLS settings per destination |
| `Gateway` | Configure L4-L7 load balancer at mesh edge (ingress/egress) |
| `ServiceEntry` | Register external services into the mesh |
| `Sidecar` | Limit scope of sidecar proxy (egress listeners, imported namespaces) |
| `EnvoyFilter` | Direct Envoy config patching (advanced, use sparingly) |
| `WorkloadEntry` | Register VM workloads into the mesh |
| `WorkloadGroup` | Template for WorkloadEntry auto-registration |
| `ProxyConfig` | Per-workload proxy configuration overrides |

### Security
| CRD | Purpose |
|-----|---------|
| `PeerAuthentication` | mTLS mode per mesh/namespace/workload |
| `RequestAuthentication` | JWT validation rules |
| `AuthorizationPolicy` | L4/L7 access control (allow/deny/custom) |

### Observability
| CRD | Purpose |
|-----|---------|
| `Telemetry` | Configure metrics, access logs, and tracing per workload/namespace |
| `WasmPlugin` | Extend Envoy with WebAssembly plugins |

## Essential istioctl Commands

```bash
# Installation
istioctl install --set profile=demo       # install with demo profile
istioctl install -f custom-iop.yaml       # install from IstioOperator file
istioctl verify-install                    # verify installation

# Diagnostics
istioctl analyze                           # detect config issues in cluster
istioctl analyze -n my-namespace           # analyze specific namespace
istioctl proxy-status                      # sync status of all proxies
istioctl proxy-config routes <pod>         # view Envoy route config
istioctl proxy-config clusters <pod>       # view Envoy cluster config
istioctl proxy-config endpoints <pod>      # view Envoy endpoint config
istioctl proxy-config listeners <pod>      # view Envoy listener config
istioctl proxy-config log <pod> --level debug  # set Envoy log level

# Debugging
istioctl x describe pod <pod>              # describe Istio config affecting pod
istioctl x authz check <pod>              # check authorization policy
istioctl bug-report                        # generate diagnostic bundle

# Sidecar injection
kubectl label namespace <ns> istio-injection=enabled
kubectl label namespace <ns> istio.io/rev=<revision>  # revision-based
```

## Quick Configuration Patterns

### Basic VirtualService + DestinationRule

```yaml
apiVersion: networking.istio.io/v1
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v2
      weight: 80
    - destination:
        host: reviews
        subset: v1
      weight: 20
---
apiVersion: networking.istio.io/v1
kind: DestinationRule
metadata:
  name: reviews
spec:
  host: reviews
  trafficPolicy:
    connectionPool:
      tcp:
        maxConnections: 100
      http:
        h2UpgradePolicy: DEFAULT
        http1MaxPendingRequests: 100
        http2MaxRequests: 1000
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
```

### Strict mTLS Mesh-Wide

```yaml
apiVersion: security.istio.io/v1
kind: PeerAuthentication
metadata:
  name: default
  namespace: istio-system   # mesh-wide when in istio-system
spec:
  mtls:
    mode: STRICT
```

## Reference Documents

Load these as needed based on the specific topic:

| Topic | File | When to read |
|-------|------|-------------|
| **Introduction & Concepts** | [references/introduction.md](references/introduction.md) | Service mesh concepts, why Istio, architecture deep-dive, Envoy internals, deployment models (sidecar vs ambient) (Ch 1) |
| **Core Components** | [references/core-components.md](references/core-components.md) | istiod internals, Pilot/Citadel/Galley, xDS API, Envoy proxy architecture, sidecar injection, init containers, CNI plugin (Ch 2) |
| **Traffic Management** | [references/traffic-management.md](references/traffic-management.md) | VirtualService, DestinationRule, Gateway, ServiceEntry, routing rules, traffic splitting, load balancing algorithms, connection pooling (Ch 3) |
| **Advanced Traffic** | [references/advanced-traffic.md](references/advanced-traffic.md) | Canary deployments, circuit breaking, fault injection, retries, timeouts, mirroring, rate limiting, locality load balancing, multi-cluster routing (Ch 4) |
| **Security** | [references/security.md](references/security.md) | mTLS, PeerAuthentication, RequestAuthentication, AuthorizationPolicy, JWT validation, SPIFFE identity, certificate management, zero-trust patterns (Ch 5) |
| **Observability Foundations** | [references/observability-foundations.md](references/observability-foundations.md) | Distributed tracing (Jaeger/Zipkin), metrics (Prometheus), Telemetry API, trace context propagation, custom metrics, span configuration (Ch 6) |
| **Visualization & Analysis** | [references/visualization.md](references/visualization.md) | Grafana dashboards, Kiali service graph, access logging, log configuration, EFK/Loki integration, alerting patterns (Ch 7) |
| **Production Deployment** | [references/production.md](references/production.md) | Installation profiles, IstioOperator, revision-based upgrades, canary control plane, multi-cluster, performance tuning, resource limits, scaling istiod (Ch 8) |
| **Custom Plugins** | [references/custom-plugins.md](references/custom-plugins.md) | Wasm plugins, WasmPlugin CRD, Envoy filters, Lua filters, ext_authz, rate limit service, building custom Wasm with Rust/Go/C++ (Ch 9) |
| **Future & Trends** | [references/future-trends.md](references/future-trends.md) | Ambient mesh (ztunnel, waypoint proxies), Istio Gateway API, sidecarless architecture, eBPF integration, multi-cluster federation evolution (Ch 10) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wbunker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
