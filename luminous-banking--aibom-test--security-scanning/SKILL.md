---
name: security-scanning
description: Run security scans on code using Snyk tools to identify vulnerabilities. Use when the user asks about security issues, wants to scan code for vulnerabilities, mentions Snyk, or after generating new code. Use when this capability is needed.
metadata:
  author: luminous-banking
---

# Security Scanning

Perform comprehensive security scans on codebases using Snyk tools.

## Quick Start

When the user needs security scanning:

1. Determine the scan type needed
2. Run the appropriate Snyk scan
3. Analyze results and report findings
4. Suggest remediation for any issues found

## Scan Types

### Static Application Security Testing (SAST)
For scanning source code:

```bash
# Use snyk_code_scan for first-party code
snyk_code_scan path="/path/to/project"
```

Best for: Python, JavaScript, TypeScript, Java, Go, and other supported languages.

### Software Composition Analysis (SCA)
For scanning dependencies:

```bash
# Use snyk_sca_scan for open-source dependencies
snyk_sca_scan path="/path/to/project"
```

Best for: Identifying vulnerable packages in requirements.txt, package.json, etc.

### Infrastructure as Code (IaC)
For scanning cloud configurations:

```bash
# Use snyk_iac_scan for Terraform, CloudFormation, Kubernetes
snyk_iac_scan path="/path/to/infrastructure"
```

Best for: Terraform files, Kubernetes manifests, Dockerfiles.

### Container Scanning
For scanning Docker images:

```bash
# Use snyk_container_scan for container images
snyk_container_scan image="image-name:tag"
```

## Workflow

1. **Identify scope**: What needs scanning (code, deps, IaC, containers)?
2. **Run scan**: Execute the appropriate Snyk tool
3. **Review results**: Analyze severity levels (critical, high, medium, low)
4. **Prioritize**: Focus on critical and high severity issues first
5. **Remediate**: Fix issues and rescan to verify

## Severity Filtering

Use `severity_threshold` to filter results:
- `critical` - Only critical vulnerabilities
- `high` - High and above
- `medium` - Medium and above
- `low` - All vulnerabilities

## Post-Scan Actions

After finding issues:

1. **Critical/High**: Attempt immediate fix if possible
2. **Medium**: Document and plan remediation
3. **Low**: Note for future cleanup

Always rescan after making fixes to confirm remediation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luminous-banking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
