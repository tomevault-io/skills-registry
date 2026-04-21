---
name: aria-securityscan
description: 🔒 Security scanning and vulnerability detection for DevSecOps Use when this capability is needed.
metadata:
  author: najia-afk
---

# aria-securityscan

Security scanning and vulnerability detection. Scan files/directories for security issues, check dependencies for known CVEs, and audit Dockerfiles.

## Usage

```bash
exec python3 /app/skills/run_skill.py security_scan <function> '<json_args>'
```

## Functions

### scan_file
Scan a single file for security issues.

```bash
exec python3 /app/skills/run_skill.py security_scan scan_file '{"path": "/app/app.py"}'
```

### scan_directory
Scan a directory recursively for security issues.

```bash
exec python3 /app/skills/run_skill.py security_scan scan_directory '{"path": "/app/workspace"}'
```

### check_dependencies
Check Python dependencies for known vulnerabilities.

```bash
exec python3 /app/skills/run_skill.py security_scan check_dependencies '{"requirements_path": "/app/requirements.txt"}'
```

### audit_docker
Audit a Dockerfile for security best practices.

```bash
exec python3 /app/skills/run_skill.py security_scan audit_docker '{"path": "/app/Dockerfile"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/najia-afk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
