---
name: source
description: Manage educational source registry and site scanning. Use when discovering, scanning, or reviewing web sources for pedagogical content. Keywords - source, scraper, scanner, registry, site, veille, decouvrir, analyser. Use when this capability is needed.
metadata:
  author: bilalyezzagit
---

# Source Intelligence

Gestion du registre de sources pedagogiques web. Decouverte, scan detaille et suivi des sites educatifs pour le curriculum tunisien.

## Commands

| Command | Usage | Description |
|---------|-------|-------------|
| `/source scan <url>` | Scanner un site | Analyse un site web et genere sa fiche detaillee |
| `/source discover` | Decouvrir | Recherche de nouvelles sources sur le web |
| `/source status` | Tableau de bord | Affiche l'etat du registre |

## Routing

Based on `$ARGUMENTS`:

- **Starts with "scan"**: See [actions/scan.md](actions/scan.md)
- **Starts with "discover"**: See [actions/discover.md](actions/discover.md)
- **Starts with "status"** or empty: See [actions/status.md](actions/status.md)

## Storage

- **Registry** : `docs/content-intelligence/sources/registry.md`
- **Fiches sites** : `docs/content-intelligence/sources/sites/{slug}.md`

## Modules couverts

Le registre couvre le curriculum tunisien : 1ere annee a Terminale, sections Math et Sciences.
Les topics suivent le vocabulaire controle du projet (voir SKILL content).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bilalyezzagit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
