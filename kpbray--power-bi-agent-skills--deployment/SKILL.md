---
name: deployment
description: Implements CI/CD pipelines and DevOps practices for Power BI. Use for automated validation, testing, and deployment of PBIP projects. Use when this capability is needed.
metadata:
  author: kpbray
---

# Deployment Skill

This skill helps implement CI/CD pipelines and DevOps practices for Power BI Desktop Projects (PBIP).

## When to Use This Skill

- Setting up automated validation pipelines
- Creating deployment workflows
- Implementing BPA checks in CI
- Configuring environment promotion
- Automating Power BI Service deployments
- Managing PBIP projects in source control

## DevOps Overview

### Pipeline Stages

```
┌─────────┐    ┌──────────┐    ┌─────────┐    ┌────────┐
│  Build  │ →  │ Validate │ →  │  Test   │ →  │ Deploy │
└─────────┘    └──────────┘    └─────────┘    └────────┘
     │              │               │              │
   Compile      BPA Rules       RLS Test      Publish to
   TMDL/PBIR    DAX Check      Data Check    Power BI
```

### Tools

| Tool | Purpose |
|------|---------|
| **Tabular Editor CLI** | TMDL validation, BPA checks |
| **Power BI REST API** | Publish, refresh, admin |
| **pbi-tools** | Alternative PBIX/PBIP tooling |
| **Fabric Git Integration** | Native Git support |

## Git Branching Strategy

### Recommended: GitFlow for Power BI

```
main (production)
  ↑
release/v1.0
  ↑
develop
  ↑
feature/add-sales-measures
```

### Branch Protection Rules

```yaml
# GitHub branch protection
main:
  required_reviews: 2
  require_status_checks: true
  required_checks:
    - bpa-validation
    - tmdl-syntax
develop:
  required_reviews: 1
  require_status_checks: true
```

### Commit Message Convention

```
<type>(<scope>): <description>

Types:
- feat: New feature
- fix: Bug fix
- refactor: Code refactoring
- docs: Documentation
- test: Tests
- ci: CI/CD changes

Examples:
feat(dax): add YoY% time intelligence measures
fix(model): correct relationship cardinality
refactor(query): optimize Sales query for folding
```

## GitHub Actions Pipelines

### Basic Validation Pipeline

```yaml
# .github/workflows/validate.yml
name: Validate PBIP

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  validate:
    runs-on: windows-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Install Tabular Editor
        run: |
          choco install tabular-editor -y

      - name: Validate TMDL Syntax
        run: |
          # Find semantic model folder
          $modelPath = Get-ChildItem -Recurse -Filter "*.pbism" |
            Select-Object -First 1 |
            Split-Path -Parent

          # Validate with Tabular Editor
          TabularEditor.exe "$modelPath" -S

      - name: Run BPA Rules
        run: |
          $modelPath = Get-ChildItem -Recurse -Filter "*.pbism" |
            Select-Object -First 1 |
            Split-Path -Parent

          # Run BPA with rules file
          TabularEditor.exe "$modelPath" `
            -A "BPA-Rules.json" `
            -V
        continue-on-error: false
```

### Full CI/CD Pipeline

```yaml
# .github/workflows/cicd.yml
name: Power BI CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

env:
  TENANT_ID: ${{ secrets.PBI_TENANT_ID }}
  CLIENT_ID: ${{ secrets.PBI_CLIENT_ID }}
  CLIENT_SECRET: ${{ secrets.PBI_CLIENT_SECRET }}
  WORKSPACE_ID: ${{ secrets.PBI_WORKSPACE_ID }}

jobs:
  validate:
    runs-on: windows-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Tabular Editor
        run: choco install tabular-editor -y

      - name: Validate TMDL
        run: |
          $model = (Get-ChildItem -Recurse -Filter "definition.pbism" |
            Select-Object -First 1).DirectoryName
          TabularEditor.exe $model -S

      - name: Run BPA
        run: |
          $model = (Get-ChildItem -Recurse -Filter "definition.pbism" |
            Select-Object -First 1).DirectoryName
          TabularEditor.exe $model -A BPA-Rules.json -V

  deploy:
    needs: validate
    if: github.ref == 'refs/heads/main'
    runs-on: windows-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Power BI Tools
        run: |
          Install-Module -Name MicrosoftPowerBIMgmt -Force -Scope CurrentUser

      - name: Authenticate to Power BI
        run: |
          $password = ConvertTo-SecureString "$env:CLIENT_SECRET" -AsPlainText -Force
          $credential = New-Object System.Management.Automation.PSCredential($env:CLIENT_ID, $password)
          Connect-PowerBIServiceAccount -ServicePrincipal -Credential $credential -TenantId $env:TENANT_ID

      - name: Deploy to Workspace
        run: |
          # Deploy using Power BI REST API
          # Note: Direct PBIP deployment requires Fabric Git integration
          # This example shows dataset refresh trigger
          Invoke-PowerBIRestMethod -Method POST `
            -Url "groups/$env:WORKSPACE_ID/datasets/{dataset-id}/refreshes"
```

## Azure DevOps Pipelines

### Validation Pipeline

```yaml
# azure-pipelines.yml
trigger:
  - main
  - develop

pool:
  vmImage: 'windows-latest'

stages:
  - stage: Validate
    displayName: 'Validate PBIP'
    jobs:
      - job: ValidateTMDL
        displayName: 'Validate TMDL Syntax'
        steps:
          - task: PowerShell@2
            displayName: 'Install Tabular Editor'
            inputs:
              targetType: 'inline'
              script: |
                choco install tabular-editor -y

          - task: PowerShell@2
            displayName: 'Validate Model'
            inputs:
              targetType: 'inline'
              script: |
                $modelPath = Get-ChildItem -Recurse -Filter "definition.pbism" |
                  Select-Object -First 1 |
                  Split-Path -Parent
                TabularEditor.exe $modelPath -S

          - task: PowerShell@2
            displayName: 'Run BPA'
            inputs:
              targetType: 'inline'
              script: |
                $modelPath = Get-ChildItem -Recurse -Filter "definition.pbism" |
                  Select-Object -First 1 |
                  Split-Path -Parent
                TabularEditor.exe $modelPath -A "$(Build.SourcesDirectory)/BPA-Rules.json" -V

  - stage: Deploy
    displayName: 'Deploy to Power BI'
    dependsOn: Validate
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
    jobs:
      - deployment: DeployToPowerBI
        displayName: 'Deploy to Production'
        environment: 'Production'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: PowerShell@2
                  displayName: 'Deploy to Service'
                  inputs:
                    targetType: 'inline'
                    script: |
                      # Deployment script here
                  env:
                    PBI_TENANT_ID: $(PBI_TENANT_ID)
                    PBI_CLIENT_ID: $(PBI_CLIENT_ID)
                    PBI_CLIENT_SECRET: $(PBI_CLIENT_SECRET)
```

## BPA Rules File

### Standard Rules (BPA-Rules.json)

```json
{
  "rules": [
    {
      "id": "NO_IMPLICIT_MEASURES",
      "name": "No Implicit Measures",
      "category": "Performance",
      "severity": 3,
      "scope": "Model",
      "expression": "Model.DiscourageImplicitMeasures == true",
      "fixExpression": "Model.DiscourageImplicitMeasures = true",
      "description": "Implicit measures should be disabled"
    },
    {
      "id": "MEASURE_DESCRIPTION",
      "name": "Measures Should Have Descriptions",
      "category": "Documentation",
      "severity": 2,
      "scope": "Measure",
      "expression": "!string.IsNullOrWhiteSpace(Description)",
      "description": "All measures should have a description"
    },
    {
      "id": "NO_BIDIRECTIONAL",
      "name": "Avoid Bidirectional Relationships",
      "category": "Performance",
      "severity": 2,
      "scope": "Relationship",
      "expression": "CrossFilteringBehavior != CrossFilteringBehavior.BothDirections",
      "description": "Bidirectional relationships impact performance"
    },
    {
      "id": "USE_DIVIDE",
      "name": "Use DIVIDE Instead of /",
      "category": "DAX",
      "severity": 2,
      "scope": "Measure",
      "expression": "!Expression.Contains(\"/\")",
      "description": "Use DIVIDE function for safe division"
    },
    {
      "id": "HIDDEN_KEY_COLUMNS",
      "name": "Key Columns Should Be Hidden",
      "category": "Formatting",
      "severity": 1,
      "scope": "Column",
      "expression": "!(Name.EndsWith(\"Key\") || Name.EndsWith(\"ID\")) || IsHidden",
      "description": "Key and ID columns should be hidden from users"
    },
    {
      "id": "NO_UNUSED_COLUMNS",
      "name": "Remove Unused Columns",
      "category": "Maintenance",
      "severity": 1,
      "scope": "Column",
      "expression": "ReferencedBy.Count > 0 || IsHidden == false",
      "description": "Unused hidden columns should be removed"
    }
  ]
}
```

## Tabular Editor CLI Reference

### Common Commands

```bash
# Validate TMDL syntax
TabularEditor.exe "path/to/Model" -S

# Run BPA with rules file
TabularEditor.exe "path/to/Model" -A "BPA-Rules.json" -V

# Save model (useful for formatting)
TabularEditor.exe "path/to/Model" -S "path/to/Model"

# Deploy to XMLA endpoint
TabularEditor.exe "path/to/Model" -D "powerbi://api.powerbi.com/v1.0/myorg/WorkspaceName" "DatasetName"

# Generate documentation
TabularEditor.exe "path/to/Model" -SCRIPT "GenerateDocs.cs"
```

### Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | Validation errors |
| 2 | BPA violations (warning) |
| 3 | BPA violations (error) |

## Power BI REST API

### Authentication

```powershell
# Service Principal Authentication
$body = @{
    grant_type    = "client_credentials"
    client_id     = $clientId
    client_secret = $clientSecret
    resource      = "https://analysis.windows.net/powerbi/api"
}
$token = Invoke-RestMethod -Uri "https://login.microsoftonline.com/$tenantId/oauth2/token" -Method Post -Body $body
$headers = @{ Authorization = "Bearer $($token.access_token)" }
```

### Common API Calls

```powershell
# Get Workspaces
Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups" -Headers $headers

# Get Datasets in Workspace
Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/datasets" -Headers $headers

# Trigger Refresh
Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/datasets/$datasetId/refreshes" -Method Post -Headers $headers

# Get Refresh History
Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/datasets/$datasetId/refreshes" -Headers $headers

# Update Parameters
$body = @{
    updateDetails = @(
        @{ name = "ServerName"; newValue = "prod-server.database.windows.net" }
    )
} | ConvertTo-Json
Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/datasets/$datasetId/Default.UpdateParameters" -Method Post -Headers $headers -Body $body -ContentType "application/json"
```

## Fabric Git Integration

### Enable Git Integration

1. Workspace Settings > Git integration
2. Connect to Azure DevOps or GitHub
3. Select repository and branch
4. Configure folder mapping

### Sync Workflow

```
Local Development
      ↓
   git push
      ↓
GitHub/Azure DevOps
      ↓
  CI Validation
      ↓
   Merge to main
      ↓
Fabric Git Sync
      ↓
Power BI Service
```

### .gitignore for Fabric

```gitignore
# Fabric-specific
.pbi/
**/localSettings.json
**/cache.abf

# Don't commit
*.pbix
*.pbit

# IDE
.vs/
.vscode/
```

## Environment Promotion

### Environment Strategy

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ Development │ →   │   Staging   │ →   │ Production  │
│  Workspace  │     │  Workspace  │     │  Workspace  │
└─────────────┘     └─────────────┘     └─────────────┘
   feature/*           develop             main
```

### Parameter-Based Promotion

Use parameters for environment-specific values:

```tmdl
expression ServerName = "dev-server.database.windows.net" meta [IsParameterQuery=true, Type="Text"]
expression DatabaseName = "SalesDB_Dev" meta [IsParameterQuery=true, Type="Text"]
```

Update via API during deployment:

```powershell
# Update parameters for production
$body = @{
    updateDetails = @(
        @{ name = "ServerName"; newValue = "prod-server.database.windows.net" },
        @{ name = "DatabaseName"; newValue = "SalesDB_Prod" }
    )
} | ConvertTo-Json

Invoke-RestMethod -Uri ".../datasets/$datasetId/Default.UpdateParameters" `
    -Method Post -Headers $headers -Body $body -ContentType "application/json"
```

## Secrets Management

### GitHub Secrets

```yaml
# In workflow
env:
  TENANT_ID: ${{ secrets.PBI_TENANT_ID }}
  CLIENT_ID: ${{ secrets.PBI_CLIENT_ID }}
  CLIENT_SECRET: ${{ secrets.PBI_CLIENT_SECRET }}
```

### Azure Key Vault

```yaml
# Azure DevOps with Key Vault
- task: AzureKeyVault@2
  inputs:
    azureSubscription: 'ServiceConnection'
    KeyVaultName: 'pbi-secrets-vault'
    SecretsFilter: 'PBI-ClientId,PBI-ClientSecret'
```

### Never Commit

```gitignore
# Secrets - NEVER commit
.env
*.secret
credentials.json
appsettings.Production.json
```

## Boundaries and Constraints

### DO
- Automate BPA checks in every PR
- Use service principals for CI/CD
- Store secrets in secure vaults
- Validate TMDL syntax before merge
- Use parameters for environment differences
- Document deployment procedures

### DO NOT
- Never commit credentials
- Don't skip validation for "quick fixes"
- Avoid deploying directly to production
- Don't use personal accounts in pipelines
- Never force-push to main branch

## Workflow Integration

After setting up deployment:
1. **Validate locally** - Run BPA before commit
2. **Push to feature branch** - CI validates automatically
3. **Create PR** - Requires validation pass
4. **Merge to develop** - Deploys to Dev workspace
5. **Merge to main** - Deploys to Production

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kpbray) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
