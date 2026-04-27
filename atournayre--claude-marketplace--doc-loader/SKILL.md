---
name: doc-loader
description: > Use when this capability is needed.
metadata:
  author: atournayre
---

# Documentation Loader Skill

## Instructions à Exécuter

**IMPORTANT : Exécute ce workflow étape par étape :**


## Usage
```
/doc:framework-load <framework> [version]
```

## Frameworks supportés

| Framework | Agent | Path |
|-----------|-------|------|
| symfony | symfony-docs-scraper | docs/symfony/[version]/ |
| api-platform | api-platform-docs-scraper | docs/api-platform/[version]/ |
| meilisearch | meilisearch-docs-scraper | docs/meilisearch/[version]/ |
| atournayre-framework | atournayre-framework-docs-scraper | docs/atournayre-framework/[version]/ |
| claude | claude-docs-scraper | docs/claude/ |

## Workflow

1. Parser arguments (framework, version)
2. Vérifier README contenant les URLs
3. Gérer cache (24h par défaut)
4. Déléguer à agent scraper spécialisé
5. Générer statistiques finales

## Cache

- Fichiers < 24h : ignorés
- Fichiers > 24h : supprimés et rechargés
- Délai 2s entre requêtes (anti rate-limit)

## Error Handling

- Framework non supporté → ARRÊT
- README introuvable → ARRÊT
- Échec scraping URL → WARNING (continue)

## References

- [Scripts de workflow](references/workflow-scripts.md) - Scripts bash et gestion rate limiting

## Notes
- Fichiers markdown nommés depuis URL
- Support multi-version

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/atournayre) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
