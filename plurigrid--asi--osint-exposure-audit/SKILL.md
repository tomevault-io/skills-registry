---
name: osint-exposure-audit
description: > Use when this capability is needed.
metadata:
  author: plurigrid
---

# OSINT Exposure Audit

Digital dumpster diving — systematically cataloguing what an organization leaks
publicly that enables attacks. Everything an adversary can learn without ever
sending a packet to your infrastructure.

## When to Use

- External attack surface assessment
- Pre-pentest reconnaissance review
- Data leakage auditing
- Breach impact assessment
- Supply chain exposure analysis
- Merger/acquisition security due diligence
- Continuous monitoring of organizational exposure drift

## Exposure Taxonomy

### Code Repositories
- Leaked secrets in git history (keys, tokens, passwords in old commits)
- `.env` files, `docker-compose.yml` with credentials committed to public repos
- API keys and service account credentials in source
- Internal URLs, hostnames, and IP ranges in IaC (Terraform, CloudFormation)
- CI/CD pipeline configs exposing infrastructure details

### Document Metadata
- Author names and usernames in PDF/Office document properties
- Software versions (Adobe, Office builds) revealing patch levels
- Internal file paths (`C:\Users\jsmith\Documents\...`) in document metadata
- EXIF data in images: GPS coordinates, device info, timestamps
- Printer/scanner metadata in published documents

### DNS and Infrastructure
- Subdomain enumeration via brute-force, zone transfers, passive DNS
- Certificate transparency logs revealing internal service names
- Historical DNS records exposing migrations and old infrastructure
- Cloud storage buckets (S3, GCS, Azure Blob) with predictable names
- ASN mapping to identify full IP space ownership
- Reverse DNS revealing naming conventions

### Credential Exposure
- Breach database correlation (email domains in known breaches)
- Paste sites (Pastebin, GitHub Gists) containing credentials
- Credential stuffing lists with organization email addresses
- Leaked password patterns revealing policy (length, complexity, rotation)
- Exposed `.htpasswd`, `web.config`, or similar auth files

### Social Media Intelligence
- Employee names, roles, reporting structure from LinkedIn
- Technology stack hints from employee profiles and endorsements
- Org chart reconstruction from public profiles
- Job postings revealing specific technologies, versions, and vendors
- Conference talks and slides disclosing architecture details
- Geolocation of facilities from employee posts

### Web Archives
- Wayback Machine snapshots of removed pages, old API documentation
- Deprecated endpoints still responding in production
- Removed job postings revealing past security concerns
- Old sitemaps and `robots.txt` exposing hidden paths
- Cached versions of pages taken down after incidents

### Supply Chain
- Third-party vendor exposure (shared credentials, connected services)
- Dependency confusion potential in public package registries
- Internal package names leaked in `package.json`, `requirements.txt`
- Vendor security posture reflecting on the organization
- Open-source contributions revealing internal tooling

## Audit Methodology

### Phase 1: Passive Reconnaissance
1. Domain and infrastructure enumeration (subdomains, IPs, ASNs, cloud resources)
2. Certificate transparency log analysis for all owned domains
3. Passive DNS collection and historical record review
4. Search engine dorking for exposed files and directories

### Phase 2: Code and Document Analysis
5. Git repository scanning — secrets in full commit history
6. Exposed `.git` directory detection on web-facing servers
7. Document metadata extraction from all public-facing files (PDFs, DOCX, XLSX)
8. Source map and debug artifact discovery

### Phase 3: Credential and Identity Exposure
9. Credential breach correlation (HaveIBeenPwned, breach compilations)
10. Paste site monitoring for organization-related dumps
11. Email address harvesting and employee enumeration

### Phase 4: Active Surface Mapping
12. Cloud storage discovery and permission testing
13. Job posting and social media intelligence gathering
14. Web archive analysis for leaked or removed content
15. Third-party service enumeration (SaaS, APIs, webhooks)

## Tool Reference

| Category       | Tools                                                      |
|---------------|-------------------------------------------------------------|
| Infrastructure | `amass`, `subfinder`, `dnsx`, `httpx`, `nuclei`, Shodan, Censys |
| Git/Code       | `truffleHog`, `gitleaks`, `git-secrets`, GitHub/GitLab search dorks |
| Documents      | `exiftool`, FOCA, `metagoofil`                             |
| Credentials    | `h8mail`, Dehashed API, PWNDB                              |
| Web            | `gau` (getallurls), `waybackurls`, `katana`                |
| Cloud          | `cloud_enum`, `S3Scanner`, `GCPBucketBrute`                |
| OSINT Frameworks | `spiderfoot`, `recon-ng`, `theHarvester`                 |

## Code Review Patterns

What leaks from codebases — check for these in any public repository:

- **Hardcoded credentials**: API keys, tokens, passwords in source files
- **Internal hostnames/IPs**: configuration files referencing `10.x.x.x`, `*.internal`
- **Sensitive comments**: `TODO: remove password`, `HACK: using admin creds`
- **Debug endpoints**: `/debug`, `/actuator`, `/elmah.axd` left enabled in production
- **Verbose error messages**: stack traces exposing file paths, library versions
- **Exposed `.git` directory**: full repository history accessible via web server
- **Source maps in production**: `.js.map` files reconstructing original source
- **Environment detection logic**: code revealing staging/prod URL patterns
- **Dependency manifests**: `package-lock.json`, `Pipfile.lock` pinning vulnerable versions

## Output Format

Structure findings as follows:

```
## Finding: [Title]
- **Exposure Type**: Code Repository | Document Metadata | Infrastructure | Credential | Social Media | Web Archive | Supply Chain
- **Source**: Where the exposure was discovered (URL, repo, document name)
- **Data Exposed**: Specific sensitive data found
- **Risk Level**: Critical | High | Medium | Low | Informational
- **Attack Scenario**: How an adversary would leverage this exposure
- **Remediation**:
  - Immediate: Rotate credential / Remove document / Revoke access
  - Preventive: Configure access control / Implement secret scanning / Deploy metadata scrubbing
- **Evidence**: Screenshot, hash, or sanitized excerpt (never include raw credentials in reports)
```

Aggregate findings into an exposure heat map by category to identify systemic issues
versus one-off leaks.

## Related Skills

- `social-engineering-audit` — leveraging OSINT findings for social engineering assessments
- `entry-point-analyzer` — mapping exposed services into exploitable attack paths
- `static-security-analyzer` — deep code review for vulnerabilities beyond leaked secrets
- `webapp-testing` — active testing of discovered web applications and APIs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plurigrid) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
