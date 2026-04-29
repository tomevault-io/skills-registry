---
name: devops-fullstack
description: Use when working with the specific DevOps action to perform
metadata:
  author: pluginagentmarketplace
---

# DevOps Fullstack Skill

Atomic skill for DevOps operations including containerization, CI/CD, and deployment.

## Responsibility

**Single Purpose**: Containerize, automate, and deploy fullstack applications

## Actions

### `containerize`
Create optimized Docker configuration for the application.

```typescript
// Input
{
  action: "containerize",
  container_runtime: "docker"
}

// Output
{
  success: true,
  dockerfile: "FROM node:20-alpine AS builder\n...",
  files: [
    { path: "Dockerfile", content: "..." },
    { path: "docker-compose.yml", content: "..." },
    { path: ".dockerignore", content: "..." }
  ]
}
```

### `setup_cicd`
Configure CI/CD pipeline.

### `deploy`
Deploy application to cloud platform.

### `configure_monitoring`
Set up monitoring and alerting.

## Validation Rules

```typescript
function validateParams(params: SkillParams): ValidationResult {
  if (!params.action) {
    return { valid: false, error: "action is required" };
  }

  if (params.action === 'deploy' && !params.cloud_provider) {
    return { valid: false, error: "cloud_provider required for deployment" };
  }

  return { valid: true };
}
```

## Error Handling

| Error Code | Description | Recovery |
|------------|-------------|----------|
| BUILD_FAILED | Docker build failed | Check Dockerfile syntax |
| PIPELINE_INVALID | CI/CD config invalid | Validate YAML syntax |
| DEPLOYMENT_FAILED | Deployment unsuccessful | Check credentials and resources |
| HEALTH_CHECK_FAILED | Service not healthy | Review logs and config |

## Logging Hooks

```json
{
  "on_invoke": "log.info('devops-fullstack invoked', { action, ci_platform })",
  "on_success": "log.info('DevOps operation completed', { files, duration_ms })",
  "on_error": "log.error('DevOps skill failed', { error })"
}
```

## Unit Test Template

```typescript
import { describe, it, expect } from 'vitest';
import { devopsFullstack } from './devops-fullstack';

describe('devops-fullstack skill', () => {
  describe('containerize', () => {
    it('should create multi-stage Dockerfile', async () => {
      const result = await devopsFullstack({
        action: 'containerize',
        container_runtime: 'docker'
      });

      expect(result.success).toBe(true);
      expect(result.dockerfile).toContain('AS builder');
      expect(result.dockerfile).toContain('AS runner');
    });

    it('should use non-root user', async () => {
      const result = await devopsFullstack({
        action: 'containerize'
      });

      expect(result.dockerfile).toContain('USER');
    });
  });

  describe('setup_cicd', () => {
    it('should create GitHub Actions workflow', async () => {
      const result = await devopsFullstack({
        action: 'setup_cicd',
        ci_platform: 'github-actions'
      });

      expect(result.success).toBe(true);
      expect(result.pipeline.jobs).toBeDefined();
    });
  });
});
```

## Integration

- **Bonded Agent**: 05-devops-integration
- **Upstream Skills**: All development skills
- **Downstream Skills**: fullstack-security

## Version History
| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2024-01 | Initial release |
| 2.0.0 | 2025-01 | Production-grade upgrade with GitOps patterns |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
