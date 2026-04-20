---
name: kustomize
description: This skill provides patterns and best practices for using Kustomize to manage Kubernetes configurations, including overlays, patches, and resource organization. Use when this capability is needed.
metadata:
  author: willejs
---

# Kustomize Patterns and Best Practices

## Directory Structure

Always organize kustomize projects using this standard structure:

```
base/
  kustomization.yaml
  deployment.yaml
  service.yaml
overlays/
  dev/
    kustomization.yaml
    patches/
  staging/
    kustomization.yaml
    patches/
  prod/
    kustomization.yaml
    patches/
components/
  monitoring/
    kustomization.yaml
  security/
    kustomization.yaml
```

## Core Principles

- **Base + Overlay Structure**: Use base for common resources, overlays for environment-specific modifications (dev, staging, prod)
- **Reusable Bases**: Organize resources into logical bases that can be referenced across multiple overlays
- **Components for CRDs**: Use components to manage custom resource definitions and operators that must be installed before dependent resources
- **Strategic Patching**: Modify specific fields using patches rather than duplicating entire resource definitions

## Creating New Overlays

When creating a new overlay, always:

1. Create the overlay directory under `overlays/`
2. Add a `kustomization.yaml` that references the base:
   ```yaml
   apiVersion: kustomize.config.k8s.io/v1beta1
   kind: Kustomization
   resources:
     - ../../base
   ```
3. Add environment-specific patches in a `patches/` subdirectory
4. Use `namespace:` field to set the target namespace
5. Use `namePrefix:` or `nameSuffix:` if needed for resource naming

## Patch Strategies

Choose the appropriate patch type based on the modification:

### Strategic Merge Patches (Preferred for simple modifications)
Use `patchesStrategicMerge` for straightforward field updates:
```yaml
patchesStrategicMerge:
  - patches/deployment-replicas.yaml
  - patches/service-type.yaml
```

**When to use**: Modifying simple fields like replicas, image tags, resource limits, labels, annotations

### JSON Patches (For complex or precise modifications)
Use `patches` with `target` for complex operations or when you need precise control:
```yaml
patches:
  - path: patches/add-sidecar.yaml
    target:
      kind: Deployment
      name: myapp
```

**When to use**: Adding array elements, removing fields, conditional modifications, working with CRDs

### Replacements (Kustomize v4+)
Use `replacements` instead of deprecated `vars` for cross-resource field substitution:
```yaml
replacements:
  - source:
      kind: ConfigMap
      name: app-config
      fieldPath: data.version
    targets:
      - select:
          kind: Deployment
        fieldPaths:
          - spec.template.metadata.labels.version
```

## Common Operations

### Modifying Image Tags
Use `images` field for image transformations:
```yaml
images:
  - name: myapp
    newTag: v1.2.3
  - name: nginx
    newName: registry.example.com/nginx
    newTag: 1.21
```

### Managing ConfigMaps and Secrets
Use generators to create ConfigMaps and Secrets with automatic hash suffixes:
```yaml
configMapGenerator:
  - name: app-config
    files:
      - config.properties
    literals:
      - LOG_LEVEL=info

secretGenerator:
  - name: db-credentials
    envs:
      - secrets.env
```

### Applying Common Metadata
Use `commonLabels` and `commonAnnotations` to apply consistent metadata:
```yaml
commonLabels:
  app: myapp
  environment: prod
  managed-by: kustomize

commonAnnotations:
  contact: team@example.com
```

### Adding New Resources
When adding a new resource to an overlay:
1. First check if it should go in the base (shared across environments)
2. If environment-specific, add the resource file to the overlay directory
3. Reference it in the overlay's `kustomization.yaml` under `resources:`
4. Prefer patching base resources over creating entirely new ones

## Validation and Testing

Always validate kustomizations before deploying:

```bash
# Build and view the output
kustomize build overlays/prod

# Validate with kubectl dry-run
kustomize build overlays/prod | kubectl apply --dry-run=client -f -

# Validate against Kubernetes schemas
kustomize build overlays/prod | kubeval

# Validate with kubeconform (faster alternative)
kustomize build overlays/prod | kubeconform -strict
```

## Best Practices

- **Keep kustomization.yaml clean**: Add comments explaining the purpose of patches and transformations
- **Avoid templating**: Kustomize is not a templating tool. Use it with Flux substitutions or Helm if you need variable interpolation
- **Limit complexity**: Avoid excessive nesting (max 2-3 levels), complex patch chains, or resource ordering dependencies
- **Component ordering**: When using components, apply them in dependency order (CRDs first, then controllers, then workloads)
- **No hardcoded secrets**: Always use secretGenerator with external sources or integrate with secret management tools
- **Namespace consistency**: Set namespace once at the overlay level, not in individual resources
- **Use kustomize CLI**: Prefer standalone kustomize CLI over kubectl kustomize for latest features

## When to Move to Helm

Consider migrating to Helm if you encounter:
- Need for extensive templating and conditionals
- Complex resource ordering requirements
- Dependency management across multiple charts
- Need for lifecycle hooks (pre-install, post-upgrade)
- Extensive value file hierarchies

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willejs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
