---
name: azure-devops-pipeline
description: | Use when this capability is needed.
metadata:
  author: hassaanch
---

# Azure DevOps Pipeline Generator

Generate production-ready Azure DevOps YAML pipelines with multi-stage builds, Docker containerization, and Helm deployments to AKS.

## What This Skill Does

- Scans repositories to identify frontend and backend project structure
- Generates optimized multi-stage Dockerfiles for common frameworks
- Creates Azure DevOps YAML pipelines with Build and Deploy stages
- Configures ACR (Azure Container Registry) image push
- Sets up Helm-based deployments to AKS (Azure Kubernetes Service)

## What This Skill Does NOT Do

- Create the actual Azure resources (ACR, AKS clusters)
- Configure Azure service connections (user must set these up)
- Write application code or fix code issues
- Handle secrets management (user responsibility)
- Manage Kubernetes cluster configuration

---

## Before Implementation

Gather context to ensure successful implementation:

| Source | Gather |
|--------|--------|
| **Codebase** | Project structure, frameworks used, existing Dockerfiles, existing pipelines |
| **Conversation** | Target environments, naming conventions, specific requirements |
| **Skill References** | Dockerfile patterns from `references/`, pipeline best practices |
| **User Guidelines** | Team naming conventions, security requirements |

---

## Required Clarifications

Ask about USER'S context before generating:

1. **Project Structure**: "Does your repo have separate frontend/backend directories, or a monorepo structure?"
2. **Frameworks**: "What frameworks are you using? (e.g., React/Vue for frontend, Node.js/Python/.NET for backend)"
3. **Azure Resources**: "What are your ACR name and AKS cluster name?"
4. **Environments**: "What environments do you need? (e.g., dev, staging, production)"

## Optional Clarifications

Ask only if relevant to the user's context:

1. **Helm Charts**: "Do you have existing Helm charts, or should I generate them?"
2. **Testing**: "Do you want to include a test stage before building?"
3. **Notifications**: "Should the pipeline send notifications (Teams, Slack) on completion?"
4. **Security Scanning**: "Do you want to include container vulnerability scanning?"

---

## Official Documentation

| Resource | URL | Use For |
|----------|-----|---------|
| Azure Pipelines YAML | https://learn.microsoft.com/en-us/azure/devops/pipelines/yaml-schema | Pipeline syntax reference |
| Docker Task | https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/docker-v2 | Docker build/push tasks |
| HelmDeploy Task | https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/helm-deploy-v0 | Helm deployment configuration |
| AKS Deployment | https://learn.microsoft.com/en-us/azure/aks/devops-pipeline | AKS CI/CD best practices |

---

## Workflow

### Phase 1: Repository Scan

```
1. Scan root directory structure
2. Identify frontend project (look for package.json with React/Vue/Angular, or next.config.js)
3. Identify backend project (look for requirements.txt, package.json with server deps, *.csproj, pom.xml)
4. Check for existing Dockerfiles
5. Check for existing azure-pipelines.yml
6. Identify build commands from package.json scripts or other config
```

### Phase 2: Dockerfile Generation

For each identified project component:

```
1. Determine runtime (Node.js, Python, .NET, Java)
2. Select appropriate base images (prefer Alpine/slim variants)
3. Generate multi-stage Dockerfile:
   - Stage 1: Build environment with dev dependencies
   - Stage 2: Production runtime with minimal footprint
4. Apply security best practices (non-root user, minimal layers)
```

**See `references/dockerfile-patterns.md` for framework-specific patterns.**

### Phase 3: Pipeline Generation

```
1. Create azure-pipelines.yml at repo root
2. Configure trigger (main/master branch)
3. Add Build stage:
   - Docker build for frontend
   - Docker build for backend
   - Push images to ACR with build ID tag
4. Add Deploy stage(s):
   - Helm upgrade/install for each environment
   - Proper dependencies between stages
   - Manual approvals for production
```

**See `references/pipeline-patterns.md` for stage configurations.**

### Phase 4: Helm Configuration

```
1. Generate Helm chart structure if not present
2. Configure values.yaml for each environment
3. Set up image pull from ACR
4. Configure service and ingress
```

**See `references/helm-deployment.md` for Helm patterns.**

---

## Detection Patterns

### Frontend Detection

| Pattern | Framework |
|---------|-----------|
| `package.json` with `react` | React |
| `package.json` with `vue` | Vue.js |
| `package.json` with `@angular/core` | Angular |
| `next.config.js` or `next.config.mjs` | Next.js |
| `nuxt.config.ts` | Nuxt.js |

### Backend Detection

| Pattern | Framework |
|---------|-----------|
| `package.json` with `express`/`fastify`/`nest` | Node.js |
| `requirements.txt` or `pyproject.toml` | Python |
| `*.csproj` with `Microsoft.NET` | .NET |
| `pom.xml` or `build.gradle` | Java |
| `go.mod` | Go |

---

## Output Files

| File | Purpose |
|------|---------|
| `frontend/Dockerfile` | Multi-stage build for frontend |
| `backend/Dockerfile` | Multi-stage build for backend |
| `azure-pipelines.yml` | CI/CD pipeline definition |
| `helm/Chart.yaml` | Helm chart metadata (if generating) |
| `helm/values.yaml` | Default Helm values |
| `helm/values-{env}.yaml` | Environment-specific values |

---

## Pipeline Structure

```yaml
trigger:
  branches:
    include:
      - main

variables:
  - group: azure-config  # Contains ACR/AKS settings

stages:
  - stage: Build
    jobs:
      - job: BuildFrontend
      - job: BuildBackend

  - stage: DeployDev
    dependsOn: Build
    condition: succeeded()

  - stage: DeployProd
    dependsOn: DeployDev
    condition: succeeded()
```

---

## Required Azure Setup (User Responsibility)

Before the pipeline works, users must:

1. **Create ACR**: Azure Container Registry for storing images
2. **Create AKS**: Azure Kubernetes Service cluster
3. **Attach ACR to AKS**: `az aks update -n <cluster> -g <rg> --attach-acr <acr>`
4. **Create Service Connection**: Azure Resource Manager connection in Azure DevOps
5. **Create Variable Group**: Store ACR, AKS, and resource group names

---

## Error Handling

| Issue | Solution |
|-------|----------|
| No frontend/backend detected | Ask user to specify directory structure |
| Unknown framework | Generate generic Dockerfile, warn user to customize |
| Existing files conflict | Ask user whether to overwrite or merge |
| Missing package.json/requirements | Generate minimal Dockerfile, note dependencies needed |

---

## Output Checklist

Before delivering, verify:

- [ ] Repository structure correctly identified
- [ ] Dockerfiles use multi-stage builds
- [ ] Dockerfiles use non-root user for security
- [ ] Pipeline has separate Build and Deploy stages
- [ ] ACR push configured with proper tags
- [ ] Helm deployment uses correct task version
- [ ] Environment-specific values files created
- [ ] Production stage has manual approval gate

---

## Reference Files

| File | When to Read |
|------|--------------|
| `references/dockerfile-patterns.md` | When generating Dockerfiles for specific frameworks |
| `references/pipeline-patterns.md` | When structuring pipeline stages and tasks |
| `references/helm-deployment.md` | When configuring Helm charts and AKS deployment |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hassaanch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
