---
name: doc-sync
description: Rappel en fin de tâche pour synchroniser la documentation après un changement significatif : nouvelle feature utilisateur, nouvelle commande, nouveau fichier/dossier à la racine, nouvelle dépendance majeure, modification d'API publique, breaking change, refonte d'architecture, nouveau workflow. Cible : CHANGELOG.md, README.md, CLAUDE.md, API_DOC.md, DESIGN_SYSTEM.md. Rappel léger, ne bloque jamais le travail. Ne se charge PAS pour : typo, fix mineur, refactor interne sans impact utilisateur, correction de commentaire, changement limité à un fichier .md, modification de test. Use when this capability is needed.
metadata:
  author: vendeesign
---

# Doc Sync — Conscience de la dette documentaire

Ce skill s'active quand des changements significatifs sont faits dans le code, pour rappeler que la documentation doit suivre.

## Quand se déclencher

- Nouvelle feature ou nouveau fichier créé
- Changement d'architecture ou de structure
- Nouvelle dépendance ajoutée
- Nouvelle commande ou script ajouté
- Changement d'API publique (endpoints, exports, props)
- Modification de la config (env vars, build, CI)

## Quand NE PAS se déclencher

- Corrections mineures : typo, formatage, renommage de variable locale
- Modifications de commentaires uniquement
- Fichiers de test ajoutés ou modifiés (sans changement de code source)
- Refactoring interne sans impact sur l'API ou la structure publique
- L'utilisateur demande explicitement de sauter la doc ("skip doc", "pas de doc")

## Quoi vérifier

### CHANGELOG.md

- Les changements sont-ils documentés ?
- Format [Keep a Changelog](https://keepachangelog.com/) respecté ?
- Catégories : Added, Changed, Fixed, Removed, Deprecated, Security
- Version et date à jour ?

### CLAUDE.md

- Section "Structure du projet" reflète l'état réel ?
- Section "Commandes" à jour (nouvelles commandes, scripts) ?
- Section "Dépendances clés" à jour ?
- Section "Contexte actuel" reflète ce qui a été fait ?
- Section "Conventions" toujours exacte ?

### README.md

- Instructions d'installation encore valides ?
- Exemples d'utilisation à jour ?
- API publique documentée correctement ?
- Prérequis à jour ?

### DESIGN_SYSTEM.md (si UI)

- Nouveaux composants documentés ?
- Tokens de design à jour ?
- Changements de couleurs, typo, spacing reflétés ?

### Templates disponibles dans `references/`

Le plugin fournit des templates prêts à copier :

| Template | Usage | Copié par `/codebloom:setup` quand... |
|----------|-------|-----------------------------|
| `DESIGN_SYSTEM.md` | Design system (couleurs, typo, spacing, composants) | Projet avec UI |
| `API_DOC.md` | Documentation d'API (endpoints, auth, erreurs) | Projet avec backend/API |
| `CHANGELOG.md` | Historique des versions (Keep a Changelog) | Toujours |

Quand un de ces fichiers manque et que le projet en aurait besoin → le signaler.

## Comportement

- **Ne pas bloquer** le travail pour la doc — signaler, pas imposer
- **Rappel léger** : "Les fichiers X et Y ont été modifiés. CHANGELOG et CLAUDE.md sont-ils à jour ?"
- **Ne pas modifier la doc automatiquement** sans validation
- Regrouper les rappels — un seul rappel en fin de tâche, pas à chaque fichier
- **Signaler les templates manquants** — si le projet a une API mais pas d'API_DOC.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vendeesign) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
