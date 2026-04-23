---
name: azure-devops
description: description: Azure DevOps CI/CD pipelines and YAML configuration Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: azure-devops
description: Azure DevOps CI/CD pipelines and YAML configuration
---

# Azure DevOps

> CI/CD pipelines with YAML configuration.

---

## Pipeline Structure

```yaml
trigger:
  - main
  - develop

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'

stages:
  - stage: Build
  - stage: Test
  - stage: Deploy
```

---

## Build Stage

```yaml
- stage: Build
  jobs:
    - job: BuildJob
      steps:
        - task: UseDotNet@2
          inputs:
            version: '8.x'
            
        - task: DotNetCoreCLI@2
          displayName: 'Restore'
          inputs:
            command: 'restore'
            
        - task: DotNetCoreCLI@2
          displayName: 'Build'
          inputs:
            command: 'build'
            arguments: '--configuration $(buildConfiguration)'
```

---

## Test Stage

```yaml
- stage: Test
  dependsOn: Build
  jobs:
    - job: TestJob
      steps:
        - task: DotNetCoreCLI@2
          displayName: 'Test'
          inputs:
            command: 'test'
            arguments: '--collect:"XPlat Code Coverage"'
```

---

## Deploy Stage

```yaml
- stage: Deploy
  dependsOn: Test
  condition: eq(variables['Build.SourceBranch'], 'refs/heads/main')
  jobs:
    - deployment: DeployJob
      environment: 'production'
      strategy:
        runOnce:
          deploy:
            steps:
              - task: AzureWebApp@1
                inputs:
                  appName: 'my-app'
```

---

## KeyVault Integration

```yaml
- task: AzureKeyVault@2
  inputs:
    azureSubscription: 'MySubscription'
    KeyVaultName: 'MyKeyVault'
    SecretsFilter: '*'
    RunAsPreJob: true
```

---

## Approval Gates

Configure in Azure DevOps UI:
1. Environments → Production
2. Approvals and checks
3. Add approvers

---

## DO / DON'T

| ✅ Do | ❌ Don't |
|-------|---------|
| YAML pipelines | Classic editor |
| Environment approvals | Auto-deploy to prod |
| KeyVault for secrets | Variable secrets |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
