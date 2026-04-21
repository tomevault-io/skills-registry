---
name: argocd-helm
description: ArgoCD and Helm expert skill. Use when deploying applications with ArgoCD, creating or reviewing Helm charts, designing GitOps workflows, managing ApplicationSets, multi-cluster deployments, progressive delivery with Argo Rollouts, troubleshooting sync issues, secrets management (SOPS, External Secrets Operator), and Kubernetes manifest management. Covers ArgoCD 3.x and Helm 3.x best practices. Use when this capability is needed.
metadata:
  author: mujez
---

You are operating as a Principal GitOps Engineer with 10+ years of Kubernetes experience, specializing in ArgoCD and Helm for production deployments.

## ArgoCD 3.x Setup & Architecture

### Quick Install
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v3.1.9/manifests/install.yaml
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### ArgoCD 3.x Breaking Changes
- Annotation-based tracking is now default (was labels)
- RBAC logs enforcement enabled by default
- Legacy metrics removed
- Fine-grained RBAC (per-resource permissions)
- Secrets operators endorsement

### Application Manifest
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: default
  source:
    repoURL: https://github.com/org/repo.git
    targetRevision: HEAD
    path: k8s/overlays/production
    # For Helm:
    # helm:
    #   valueFiles:
    #     - values-prod.yaml
    #   parameters:
    #     - name: image.tag
    #       value: v1.0.0
  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
      allowEmpty: false
    syncOptions:
      - CreateNamespace=true
      - PrunePropagationPolicy=foreground
      - PruneLast=true
    retry:
      limit: 5
      backoff:
        duration: 5s
        factor: 2
        maxDuration: 3m
```

### Sync Waves & Hooks
```yaml
# Resources sync in wave order (lowest first)
metadata:
  annotations:
    argocd.argoproj.io/sync-wave: "-1"  # Before main resources

# Hook types: PreSync, Sync, PostSync, SyncFail
metadata:
  annotations:
    argocd.argoproj.io/hook: PreSync
    argocd.argoproj.io/hook-delete-policy: HookSucceeded
```

## Helm Chart Development

### Chart Structure
```
mychart/
├── Chart.yaml
├── Chart.lock
├── values.yaml
├── values.schema.json      # JSON schema for values validation
├── .helmignore
├── templates/
│   ├── _helpers.tpl         # Template helpers
│   ├── NOTES.txt            # Post-install notes
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── hpa.yaml
│   ├── pdb.yaml
│   ├── serviceaccount.yaml
│   ├── configmap.yaml
│   ├── secret.yaml
│   └── tests/
│       └── test-connection.yaml
└── charts/                  # Dependencies
```

### Chart.yaml
```yaml
apiVersion: v2
name: my-service
description: A Helm chart for my-service
type: application
version: 1.0.0        # Chart version
appVersion: "2.0.0"   # App version
dependencies:
  - name: postgresql
    version: "~15.0"
    repository: https://charts.bitnami.com/bitnami
    condition: postgresql.enabled
```

### values.yaml Best Practices
```yaml
# -- Number of replicas
replicaCount: 2

image:
  # -- Container image repository
  repository: ghcr.io/org/my-service
  # -- Image pull policy
  pullPolicy: IfNotPresent
  # -- Image tag (defaults to chart appVersion)
  tag: ""

# -- Resource requests and limits
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 512Mi

# -- Autoscaling configuration
autoscaling:
  enabled: false
  minReplicas: 2
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80

# -- Pod disruption budget
podDisruptionBudget:
  enabled: true
  minAvailable: 1

# -- Service account configuration
serviceAccount:
  create: true
  annotations: {}
  name: ""

# -- Ingress configuration
ingress:
  enabled: false
  className: nginx
  annotations: {}
  hosts:
    - host: example.com
      paths:
        - path: /
          pathType: Prefix
  tls: []
```

### Template Helpers (_helpers.tpl)
```yaml
{{- define "mychart.fullname" -}}
{{- if .Values.fullnameOverride }}
{{- .Values.fullnameOverride | trunc 63 | trimSuffix "-" }}
{{- else }}
{{- $name := default .Chart.Name .Values.nameOverride }}
{{- printf "%s-%s" .Release.Name $name | trunc 63 | trimSuffix "-" }}
{{- end }}
{{- end }}

{{- define "mychart.labels" -}}
helm.sh/chart: {{ include "mychart.chart" . }}
app.kubernetes.io/name: {{ include "mychart.name" . }}
app.kubernetes.io/instance: {{ .Release.Name }}
app.kubernetes.io/version: {{ .Values.image.tag | default .Chart.AppVersion | quote }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{- end }}

{{- define "mychart.selectorLabels" -}}
app.kubernetes.io/name: {{ include "mychart.name" . }}
app.kubernetes.io/instance: {{ .Release.Name }}
{{- end }}
```

### Production Deployment Template
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    {{- include "mychart.labels" . | nindent 4 }}
spec:
  {{- if not .Values.autoscaling.enabled }}
  replicas: {{ .Values.replicaCount }}
  {{- end }}
  selector:
    matchLabels:
      {{- include "mychart.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      annotations:
        checksum/config: {{ include (print $.Template.BasePath "/configmap.yaml") . | sha256sum }}
      labels:
        {{- include "mychart.labels" . | nindent 8 }}
    spec:
      serviceAccountName: {{ include "mychart.serviceAccountName" . }}
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 1000
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: {{ .Values.service.port }}
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /healthz
              port: http
            initialDelaySeconds: 15
            periodSeconds: 20
          readinessProbe:
            httpGet:
              path: /readyz
              port: http
            initialDelaySeconds: 5
            periodSeconds: 10
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
          env:
            {{- range $key, $value := .Values.env }}
            - name: {{ $key }}
              value: {{ $value | quote }}
            {{- end }}
      {{- with .Values.nodeSelector }}
      nodeSelector:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      {{- with .Values.tolerations }}
      tolerations:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      topologySpreadConstraints:
        - maxSkew: 1
          topologyKey: topology.kubernetes.io/zone
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              {{- include "mychart.selectorLabels" . | nindent 14 }}
```

## ApplicationSets (Multi-Cluster)

### Cluster Generator
```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: my-app
  namespace: argocd
spec:
  generators:
    - clusters:
        selector:
          matchLabels:
            environment: production
  template:
    metadata:
      name: '{{name}}-my-app'
    spec:
      project: default
      source:
        repoURL: https://github.com/org/repo.git
        targetRevision: HEAD
        path: 'k8s/overlays/{{metadata.labels.environment}}'
      destination:
        server: '{{server}}'
        namespace: my-app
```

### Git Directory Generator
```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: apps
spec:
  generators:
    - git:
        repoURL: https://github.com/org/repo.git
        revision: HEAD
        directories:
          - path: apps/*
  template:
    metadata:
      name: '{{path.basename}}'
    spec:
      source:
        repoURL: https://github.com/org/repo.git
        path: '{{path}}'
      destination:
        server: https://kubernetes.default.svc
        namespace: '{{path.basename}}'
```

### Matrix Generator (Cluster x App)
```yaml
spec:
  generators:
    - matrix:
        generators:
          - clusters:
              selector:
                matchLabels:
                  environment: production
          - git:
              repoURL: https://github.com/org/repo.git
              directories:
                - path: apps/*
```

## Repository Structure

### Monorepo (App of Apps)
```
gitops-repo/
├── apps/                    # Application definitions
│   ├── frontend/
│   │   ├── base/
│   │   │   ├── deployment.yaml
│   │   │   ├── service.yaml
│   │   │   └── kustomization.yaml
│   │   └── overlays/
│   │       ├── dev/
│   │       ├── staging/
│   │       └── production/
│   └── backend/
├── infrastructure/          # Cluster infrastructure
│   ├── ingress-nginx/
│   ├── cert-manager/
│   ├── monitoring/
│   └── external-secrets/
├── argocd/                  # ArgoCD config
│   ├── projects/
│   ├── applications/
│   └── applicationsets/
└── charts/                  # Custom Helm charts
    └── my-service/
```

### Helm with ArgoCD
```yaml
# ArgoCD Application pointing to Helm chart
spec:
  source:
    repoURL: https://github.com/org/charts.git
    path: charts/my-service
    targetRevision: HEAD
    helm:
      valueFiles:
        - values.yaml
        - values-production.yaml
      parameters:
        - name: image.tag
          value: "v2.0.0"
```

## Progressive Delivery

### Argo Rollouts - Canary
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: my-app
spec:
  replicas: 5
  strategy:
    canary:
      steps:
        - setWeight: 20
        - pause: {duration: 2m}
        - analysis:
            templates:
              - templateName: success-rate
        - setWeight: 50
        - pause: {duration: 5m}
        - setWeight: 100
  selector:
    matchLabels:
      app: my-app
  template:
    # Same as Deployment template
```

### Argo Rollouts - Blue-Green
```yaml
spec:
  strategy:
    blueGreen:
      activeService: my-app-active
      previewService: my-app-preview
      autoPromotionEnabled: false
      prePromotionAnalysis:
        templates:
          - templateName: smoke-test
```

## Secrets Management

### SOPS + age (Recommended)
```bash
# Generate key
age-keygen -o key.txt

# .sops.yaml
creation_rules:
  - path_regex: .*.enc.yaml
    encrypted_regex: ^(data|stringData)$
    age: age1ql3z7hjy54pw3hyww5ayyfg...

# Encrypt
sops -e secret.yaml > secret.enc.yaml

# ArgoCD integration - install ksops or sops plugin
```

### External Secrets Operator
```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: my-secret
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: aws-secrets-manager
    kind: ClusterSecretStore
  target:
    name: my-secret
  data:
    - secretKey: password
      remoteRef:
        key: /prod/my-app/db-password
```

## Troubleshooting

### ArgoCD OutOfSync
```bash
# Check diff
argocd app diff my-app

# Force sync
argocd app sync my-app --force

# Hard refresh (clear cache)
argocd app get my-app --hard-refresh

# Check resource health
argocd app resources my-app
```

### Common Issues

| Issue | Cause | Fix |
|-------|-------|-----|
| OutOfSync loop | Mutating webhooks, server-side defaults | Add ignoreDifferences |
| Sync failed | RBAC, invalid manifests | Check `argocd app sync` logs |
| Health degraded | Probe failures, resource limits | Check pod events/logs |
| Helm values not applied | Wrong valueFiles path | Verify path in Application spec |
| Pruning unexpected | Label/annotation mismatch | Check tracking method |

### ignoreDifferences (for fields modified by controllers)
```yaml
spec:
  ignoreDifferences:
    - group: apps
      kind: Deployment
      jsonPointers:
        - /spec/replicas  # Managed by HPA
    - group: ""
      kind: Service
      jqPathExpressions:
        - .spec.clusterIP
```

## Helm Commands Quick Reference

```bash
# Create new chart
helm create my-chart

# Lint chart
helm lint my-chart/

# Template (dry-run render)
helm template my-release my-chart/ -f values-prod.yaml

# Install
helm install my-release my-chart/ -n my-namespace --create-namespace

# Upgrade
helm upgrade my-release my-chart/ -f values-prod.yaml

# Rollback
helm rollback my-release 1

# List releases
helm list -A

# Show values
helm show values my-chart/

# Dependency update
helm dependency update my-chart/

# Test
helm test my-release
```

## ArgoCD CLI Quick Reference

```bash
# Login
argocd login argocd.example.com --grpc-web

# List apps
argocd app list

# Get app details
argocd app get my-app

# Sync
argocd app sync my-app

# Diff
argocd app diff my-app

# History
argocd app history my-app

# Rollback
argocd app rollback my-app <history-id>

# Delete
argocd app delete my-app --cascade

# Manage clusters
argocd cluster add my-context
argocd cluster list

# Manage repos
argocd repo add https://github.com/org/repo.git --ssh-private-key-path ~/.ssh/id_ed25519
```

## Helm Chart Quality Checklist

- [ ] `values.schema.json` for input validation
- [ ] Resource requests and limits on all containers
- [ ] Liveness and readiness probes
- [ ] SecurityContext with non-root user
- [ ] PodDisruptionBudget for HA
- [ ] ServiceAccount with minimal permissions
- [ ] Proper `app.kubernetes.io/*` labels
- [ ] NOTES.txt with post-install instructions
- [ ] `helm test` with test pod
- [ ] NetworkPolicy when applicable
- [ ] TopologySpreadConstraints for zone distribution
- [ ] Configmap checksum annotation for auto-restart

For detailed references see [references/gitops-patterns.md](references/gitops-patterns.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mujez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
