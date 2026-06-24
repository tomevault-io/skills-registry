---
name: kubernetes-deploy
description: Kubernetes production deployment covering strategies, HPA/VPA autoscaling, resource requests/limits, ConfigMaps/Secrets, Helm charts, Kustomize overlays, Istio service mesh, ingress controllers, PVs, RBAC, network policies, PodDisruptionBudgets, and init containers/sidecar patterns. Use when this capability is needed.
metadata:
  author: AniruddhaPKawarase
---

# Enterprise Kubernetes Deployment (30+ Year Veteran)

## Deployment Strategies

```yaml
# Rolling Update: Default (sequential pod replacement)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # 1 extra pod during update (3 → 4 → 3)
      maxUnavailable: 0  # Don't remove old pods before new one ready
  template:
    spec:
      containers:
      - name: api
        image: myregistry.azurecr.io/api:v2.0.0
        readinessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 5
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 15
          periodSeconds: 20

# Blue-Green: Two full deployments
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
      version: blue
  template:
    metadata:
      labels:
        app: api
        version: blue
    spec:
      containers:
      - name: api
        image: myregistry.azurecr.io/api:v1.9.0
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
      version: green
  template:
    metadata:
      labels:
        app: api
        version: green
    spec:
      containers:
      - name: api
        image: myregistry.azurecr.io/api:v2.0.0
---
apiVersion: v1
kind: Service
metadata:
  name: api
spec:
  selector:
    app: api
    version: blue  # Switch to "green" when ready
  ports:
  - port: 80
    targetPort: 8000
```

## HPA: Horizontal Pod Autoscaling

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api
  minReplicas: 3
  maxReplicas: 100
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70  # Scale up if >70% CPU
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80  # Scale up if >80% memory
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300  # Wait 5min before scaling down
      policies:
      - type: Percent
        value: 50  # Remove 50% of pods
        periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 0  # Scale up immediately
      policies:
      - type: Percent
        value: 100  # Add 100% (double pods)
        periodSeconds: 60
```

## ConfigMaps & Secrets

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: api-config
data:
  LOG_LEVEL: "info"
  MAX_CONNECTIONS: "100"
  cache_ttl: "3600"

---
apiVersion: v1
kind: Secret
metadata:
  name: api-secrets
type: Opaque
stringData:  # Values encoded to base64 when saved
  DATABASE_URL: "postgresql://user:pass@db:5432/mydb"
  API_KEY: "sk_live_..."

---
apiVersion: v1
kind: Pod
metadata:
  name: api
spec:
  containers:
  - name: api
    image: myapp
    env:
    # From ConfigMap
    - name: LOG_LEVEL
      valueFrom:
        configMapKeyRef:
          name: api-config
          key: LOG_LEVEL
    # From Secret
    - name: DATABASE_URL
      valueFrom:
        secretKeyRef:
          name: api-secrets
          key: DATABASE_URL
    # Direct value
    - name: NODE_ENV
      value: "production"
    volumeMounts:
    - name: config
      mountPath: /etc/config
  volumes:
  - name: config
    configMap:
      name: api-config
```

## Helm Charts: Templating & Deployment

```yaml
# values.yaml
replicas: 3
image:
  repository: myregistry.azurecr.io/api
  tag: "2.0.0"
  pullPolicy: IfNotPresent

resources:
  requests:
    cpu: 500m
    memory: 256Mi
  limits:
    cpu: 1000m
    memory: 512Mi

ingress:
  enabled: true
  host: api.example.com

# templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "api.fullname" . }}
spec:
  replicas: {{ .Values.replicas }}
  template:
    spec:
      containers:
      - name: api
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        resources: {{ .Values.resources | toYaml | nindent 10 }}

# Deploy
helm install api ./chart
helm upgrade api ./chart --values production-values.yaml
helm rollback api 1  # Rollback to previous release
```

## RBAC: Role-Based Access Control

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: api-service-account

---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: api-role
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list"]
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get"]
  resourceNames: ["api-secrets"]  # Only this specific secret

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: api-rolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: api-role
subjects:
- kind: ServiceAccount
  name: api-service-account
```

## Network Policies: Microsegmentation

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-network-policy
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes:
  - Ingress
  - Egress

  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: ingress  # Only ingress-controller can reach API
    ports:
    - protocol: TCP
      port: 8000

  egress:
  - to:
    - podSelector:
        matchLabels:
          app: db  # API can reach database
    ports:
    - protocol: TCP
      port: 5432
  - to:
    - podSelector:
        matchLabels:
          app: cache  # API can reach cache
    ports:
    - protocol: TCP
      port: 6379
  # Deny all other traffic
```

## PodDisruptionBudget: High Availability

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: api-pdb
spec:
  minAvailable: 2  # Always have >=2 pods available
  selector:
    matchLabels:
      app: api

# Kubernetes respects this during:
# - Node maintenance (drain)
# - Cluster upgrades
# - Resource pressure
# Won't evict pods if it violates PDB
```

## Init Containers: Setup Tasks

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: api
spec:
  containers:
  - name: api
    image: myapp
    volumeMounts:
    - name: shared
      mountPath: /data

  initContainers:  # Run before main containers
  - name: migrate-db
    image: myapp
    command: ["python", "-m", "alembic", "upgrade", "head"]
    env:
    - name: DATABASE_URL
      valueFrom:
        secretKeyRef:
          name: api-secrets
          key: DATABASE_URL

  - name: warm-cache
    image: redis:7
    command: ["redis-cli", "ping"]  # Wait for Redis

  volumes:
  - name: shared
    emptyDir: {}

# Order: migrate-db → warm-cache → api container starts
```

## Sidecar Pattern: Observability

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: api
spec:
  containers:
  - name: api
    image: myapp
    ports:
    - containerPort: 8000
    volumeMounts:
    - name: logs
      mountPath: /var/log

  - name: sidecar-logs  # Sidecar: tail logs
    image: fluent/fluent-bit:latest
    volumeMounts:
    - name: logs
      mountPath: /var/log
    # Ship logs to ELK/Splunk

  - name: sidecar-metrics  # Sidecar: export metrics
    image: prom/node-exporter:latest
    ports:
    - containerPort: 9100

  volumes:
  - name: logs
    emptyDir: {}
```

## Kubernetes Checklist

```markdown
# Kubernetes Checklist

## Deployment
- [ ] Rolling update or blue-green strategy
- [ ] Readiness & liveness probes configured
- [ ] Resource requests/limits set
- [ ] Health checks passing

## Scaling
- [ ] HPA configured (CPU/memory metrics)
- [ ] Min/max replicas appropriate
- [ ] Scaling policies tested under load

## Configuration
- [ ] ConfigMaps for non-sensitive config
- [ ] Secrets for sensitive data
- [ ] Environment variables populated
- [ ] Secrets never logged or exposed

## Security
- [ ] RBAC roles minimal (least privilege)
- [ ] Network policies restrict traffic
- [ ] Pod security policies enforced
- [ ] Image scanning for CVEs

## Reliability
- [ ] PodDisruptionBudgets set (>1 replica)
- [ ] Init containers run before main app
- [ ] Sidecars for observability/logging
- [ ] Persistent volumes backed up

## Monitoring
- [ ] Metrics exported (Prometheus)
- [ ] Logs centralized (ELK/Splunk)
- [ ] Alerts configured
- [ ] Dashboards created
```

## Summary
Kubernetes orchestrates containers at scale. Use rolling updates or blue-green strategies for safety. Configure HPA for elasticity. Set resource limits. Use RBAC for security. Network policies enforce microsegmentation. Init containers perform setup. Sidecars add observability. PodDisruptionBudgets maintain availability during maintenance. Kubernetes is powerful and complex—automate everything with Helm/Kustomize.

---
> Source: [AniruddhaPKawarase/ai-agent-skills-toolkit](https://github.com/AniruddhaPKawarase/ai-agent-skills-toolkit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
