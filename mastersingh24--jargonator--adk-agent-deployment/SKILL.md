---
name: adk-agent-deployment
description: Guidelines for deploying agents built with the Google Agent Development Kit (ADK) to Kubernetes. Covers server modes, health checks, and VPC exposure. Use when this capability is needed.
metadata:
  author: mastersingh24
---

# ADK Agent Deployment

This skill provides best practices for containerizing and deploying ADK-based agents to Kubernetes environments like GKE.

## 1. Containerization (Docker)

Use multi-stage builds and distroless images for security and size.

```dockerfile
# Build stage
FROM golang:1.25.5-alpine AS builder
...
RUN CGO_ENABLED=0 GOOS=linux go build -o agent main.go

# Final stage
FROM gcr.io/distroless/static-debian12
COPY --from=builder /app/agent /agent
ENTRYPOINT ["/agent"]
```

## 2. Server Mode (Startup Arguments)

ADK agents often default to console mode. When deploying to K8s, explicitly set the mode to `web api webui` to ensure the process keeps running, exposes a REST interface, and provides a web interface.

When exposing the agent via a Load Balancer, you **must** provide the `--api_server_address` flag so the Web UI knows how to reach the API.

**Command Arguments:**
```yaml
args: ["web", "api", "webui", "--api_server_address", "http://<EXTERNAL_IP>/api"]
```

## 3. Health Checks (Probes)

- **HTTP Probes**: ADK Web API provides `/api` as a default route.
- **TCP Socket Probes**: Recommended for **distroless** images where local shell tools (like `wget`) are unavailable.

```yaml
readinessProbe:
  tcpSocket:
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
livenessProbe:
  tcpSocket:
    port: 8080
  initialDelaySeconds: 15
  periodSeconds: 20
```

## 4. Networking & VPC Exposure

To expose the agent to an internal VPC securely:
- Use `type: LoadBalancer`.
- Add the internal load balancer annotation.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: jargonator
  annotations:
    networking.gke.io/load-balancer-type: "Internal"
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
```

## 5. Deployment Workflow (The "IP Catch-22")

Since the Web UI requires the Load Balancer IP to communicate with the API, follow this two-step deployment:

1.  **Deploy the Service first**: Apply only the `Service` part of the manifest.
2.  **Wait for the IP**: `kubectl get svc jargonator -w` until an `EXTERNAL-IP` is assigned.
3.  **Update Deployment**: Copy the IP into the `--api_server_address` argument in the `Deployment` spec.
4.  **Deploy the rest**: Apply the `Deployment` and other resources.

## 6. Configuration (Environment Variables)

Always externalize the model connection:
- `MODEL_API_BASE`: Point to the internal model server service.
- `MODEL_NAME`: Use the fully qualified path if mounting from storage (e.g., `/models/models/google/gemma-3-27b-it`).

## 7. Observability & Scaling

Use `HorizontalPodAutoscaler` for demand-based scaling and `PodMonitoring` for Google Cloud Managed Service for Prometheus.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: jargonator-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: jargonator
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
---
apiVersion: monitoring.googleapis.com/v1
kind: PodMonitoring
metadata:
  name: jargonator-podmonitoring
spec:
  selector:
    matchLabels:
      app: jargonator
  endpoints:
  - port: http
    interval: 30s
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mastersingh24) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
