---
name: update-vulndb
description: Update the embedded build platform vulnerability database from the CVE Project's cvelistV5 repository. Use when this capability is needed.
metadata:
  author: boostsecurityio
---

Update the embedded build platform vulnerability database:

1. Run the database update: `make update-vulndb`
2. Verify the updated database compiles correctly: `make test`
3. Report how many CVEs were added or updated compared to the previous version

Note: This clones the CVE repository (sparse checkout) and processes CVE JSON files for GitHub Actions and GitLab CI vulnerabilities. The output is written to `opa/rego/external/build_platform.rego`.

---
> Source: [boostsecurityio/poutine](https://github.com/boostsecurityio/poutine) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
