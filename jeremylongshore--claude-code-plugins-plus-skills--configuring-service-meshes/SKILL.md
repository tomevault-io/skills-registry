---
name: configuring-service-meshes
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---
# Configuring Service Meshes

## Overview

Configure service meshes (Istio, Linkerd, Consul Connect) for Kubernetes microservices architectures. Generate mTLS configurations, traffic management rules (routing, splitting, mirroring), observability integrations (distributed tracing, metrics), and resilience patterns (retries, circuit breakers, timeouts).

## Prerequisites

- Kubernetes cluster accessible via `kubectl` with admin permissions
- Service mesh CLI installed: `istioctl`, `linkerd`, or `consul`
- Helm 3+ for service mesh installation charts
- Understanding of microservice communication patterns and dependencies
- Observability backend available (Jaeger, Zipkin, or Prometheus/Grafana) for tracing and metrics

## Instructions

1. Select the service mesh based on requirements: Istio for full-featured L7 control, Linkerd for lightweight simplicity, Consul Connect for multi-platform
2. Install the control plane: `istioctl install --set profile=production` or `linkerd install | kubectl apply -f -`
3. Enable sidecar injection for target namespaces: label namespaces with `istio-injection=enabled` or `linkerd.io/inject=enabled`
4. Configure mTLS: set PeerAuthentication to STRICT mode for zero-trust inter-service communication
5. Define traffic management rules: VirtualService for routing, DestinationRule for load balancing and circuit breaking
6. Set up traffic splitting for canary deployments: route a percentage of traffic to the new version
7. Configure retry policies and timeouts to improve resilience against transient failures
8. Integrate observability: connect to Jaeger/Zipkin for distributed tracing, Prometheus for metrics, and Kiali for visualization
9. Validate the mesh: verify sidecar injection, mTLS status, and traffic routing with `istioctl analyze` or `linkerd check`

## Output

- Service mesh installation manifests or Helm values
- PeerAuthentication and AuthorizationPolicy manifests for mTLS and RBAC
- VirtualService and DestinationRule manifests for traffic management
- ServiceEntry manifests for external service access
- Observability integration configuration (Jaeger, Prometheus, Kiali)

## Error Handling

| Error | Cause | Solution |
|-------|-------|---------|
| `sidecar not injected` | Namespace not labeled for injection or pod has annotation to skip | Add `istio-injection=enabled` label to namespace; check pod annotations |
| `mTLS handshake failed` | Mismatched TLS settings between services or missing certificates | Set PeerAuthentication to PERMISSIVE temporarily; check `istioctl proxy-status` |
| `503 Service Unavailable` | Circuit breaker tripped or upstream connection pool exhausted | Review DestinationRule connection pool settings; increase `maxConnections` and `http2MaxRequests` |
| `traffic not splitting correctly` | VirtualService weight percentages misconfigured | Verify weights sum to 100; check VirtualService is bound to the correct gateway/host |
| `high latency after mesh install` | Sidecar proxy adding overhead or misconfigured timeouts | Tune proxy resources; review timeout settings; check if services are using HTTP/2 |

## Examples

- "Install Istio with strict mTLS on a production cluster and configure a VirtualService for canary routing: 90% to v1, 10% to v2."
- "Set up Linkerd on a microservices cluster with automatic retries (3 attempts, 500ms timeout) and integrate with Prometheus for golden signal metrics."
- "Configure an Istio AuthorizationPolicy that allows only the frontend service to call the API gateway, blocking all other inter-service traffic."

## Resources

- Istio documentation: https://istio.io/latest/docs/
- Linkerd documentation: https://linkerd.io/2/overview/
- Consul Connect: https://developer.hashicorp.com/consul/docs/connect
- Kiali (service mesh observability): https://kiali.io/docs/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
