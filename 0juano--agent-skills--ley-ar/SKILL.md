---
name: ley-ar
description: > Use when this capability is needed.
metadata:
  author: 0juano
---

# ley-ar — Argentine Legal Database Search

Search jurisprudence, legislation, and doctrine from 4 public Argentine legal databases.

## Installation

```bash
cd {baseDir}/scripts && pip install -e . --break-system-packages -q
```

Requires Python 3.10+. Dependencies: typer, httpx, rich.

## Databases

| DB | Source | Best For | Reliability |
|---|---|---|---|
| `saij` | saij.gob.ar | National jurisprudence, legislation, doctrine | ✅ Clean JSON API |
| `csjn` | sjconsulta.csjn.gov.ar | Supreme Court summaries | ✅ HTML+JSON |
| `juba` | juba.scba.gov.ar | Buenos Aires Province decisions | ⚠️ HTML scraping |
| `juscaba` | eje.juscaba.gob.ar | CABA court cases/expedientes | ⚠️ Poor free-text |

**Default strategy:** Start with `--db saij,csjn`. Add `juba` only for PBA-specific queries. Use `juscaba` only with case IDs.

## Commands

```bash
# Search all databases (parallel)
ley search "prescripción adquisitiva"

# Filter by database(s)
ley search "daño moral" --db saij,csjn

# Limit results
ley search "phishing bancario" --db saij --limit 5

# JSON output for scripting
ley search "responsabilidad civil" --db csjn --json

# Plain text
ley search "contrato de locación" --text

# Status check
ley status
ley --version
```

## Search Tips

- Use legal terminology: "daños y perjuicios" not "accidente de auto"
- Be specific: "prescripción adquisitiva inmueble" > "prescripción"
- **"Patentes" ambiguity:** returns IP patents, not vehicle tax. Use "impuesto automotor" or "radicación automotor" instead.
- For current tax rates or alícuotas, use web search — these databases index case law, not regulatory info.

## Output

Each result: `db`, `id`, `title`, `date`, `snippet`, `url`. Default: Rich table. `--json` for structured data.

## Limitations

- JUBA depends on ASP.NET WebForms scraping — breaks if site changes
- CSJN full case text may need reCAPTCHA (summaries work)
- JUSCABA works best with case identifiers, not free-text
- No auth required — all public APIs
- Respect rate limits: avoid rapid-fire queries

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0juano) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
