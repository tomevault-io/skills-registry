---
name: generating-helm-charts
description: | Use when this capability is needed.
metadata:
  author: helixdevelopment
---
# Helm Chart Generator

This skill provides automated assistance for helm chart generator tasks.

## Prerequisites

Before using this skill, ensure:
- Helm 3+ is installed on the system
- Kubernetes cluster access is configured
- Application container images are available
- Understanding of application resource requirements
- Chart repository access (if publishing)

## Instructions

1. **Gather Requirements**: Identify application type, dependencies, configuration needs
2. **Create Chart Structure**: Generate Chart.yaml with metadata and version info
3. **Define Values**: Create values.yaml with configurable parameters and defaults
4. **Build Templates**: Generate deployment, service, ingress, and configmap templates
5. **Add Helpers**: Create _helpers.tpl for reusable template functions
6. **Configure Resources**: Set resource limits, security contexts, and health checks
7. **Test Chart**: Validate with `helm lint` and `helm template` commands
8. **Document Usage**: Add README with installation instructions and configuration options

## Output

Generates complete Helm chart structure:

```
{baseDir}/helm-charts/app-name/
├── Chart.yaml          # Chart metadata
├── values.yaml         # Default configuration
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── configmap.yaml
│   ├── _helpers.tpl    # Template helpers
│   └── NOTES.txt       # Post-install notes
├── charts/             # Dependencies
└── README.md
```

**Example Chart.yaml:**
```yaml
apiVersion: v2
name: my-app
description: Production-ready application chart
type: application
version: 1.0.0
appVersion: "1.0.0"
```

**Example values.yaml:**
```yaml
replicaCount: 3
image:
  repository: registry/app
  tag: "1.0.0"
  pullPolicy: IfNotPresent
resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 250m
    memory: 256Mi
```

## Error Handling

Common issues and solutions:

**Chart Validation Errors**
- Error: "Chart.yaml: version is required"
- Solution: Ensure Chart.yaml contains valid apiVersion, name, and version fields

**Template Rendering Failures**
- Error: "parse error in deployment.yaml"
- Solution: Validate template syntax with `helm template` and check Go template formatting

**Missing Dependencies**
- Error: "dependency not found"
- Solution: Run `helm dependency update` in chart directory

**Values Override Issues**
- Error: "failed to render values"
- Solution: Check values.yaml syntax and ensure proper YAML indentation

**Installation Failures**
- Error: "release failed: timed out waiting for condition"
- Solution: Increase timeout or check pod logs for application startup issues

## Resources

- Helm documentation: https://helm.sh/docs/
- Chart best practices guide: https://helm.sh/docs/chart_best_practices/
- Template function reference: https://helm.sh/docs/chart_template_guide/
- Example charts repository: https://github.com/helm/charts
- Chart testing guide in {baseDir}/docs/helm-testing.md

## Overview

This skill provides automated assistance for the described functionality.

## Examples

Example usage patterns will be demonstrated in context.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/helixdevelopment) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
