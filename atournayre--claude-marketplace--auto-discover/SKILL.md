---
name: devautodiscover
description: Comprendre le besoin avant développement - Mode AUTO (Phase 0) Use when this capability is needed.
metadata:
  author: atournayre
---

# Configuration de sortie

## Instructions à Exécuter

**IMPORTANT : Exécute ce workflow étape par étape :**


**IMPORTANT** : Cette commande génère un résumé de compréhension structuré et nécessite un format de sortie spécifique.

Lis le frontmatter de cette commande. Si un champ `output-style` est présent, exécute immédiatement :
```
/output-style <valeur-du-champ>
```

*Note : Une fois que le champ `output-style` sera supporté nativement par Claude Code, cette instruction pourra être supprimée.*

# Objectif

Phase 1 du workflow automatisé : comprendre le besoin utilisateur **SANS interaction**.

# Feature demandée (depuis Issue GitHub)

Récupérée depuis `.claude/data/workflows/issue-${issue_number}-dev-workflow-state.json` (Phase 0)

**Titre :** issue.title
**Description :** issue.description
**Labels :** issue.labels

# Mode automatique (zéro checkpoint)

Ce skill exécute la phase 0 en mode **AUTOMATIQUE** :
- Pas de demande de confirmation
- Validation auto de la clarté
- FAIL si ambiguïté critique

# Instructions

## 1. Analyser la demande

Déterminer le chemin du workflow state :

```bash
# Récupérer issue_number depuis le workflow state créé en Phase 0
# Note: si issue_number est disponible en contexte, l'utiliser directement
workflow_state_file=".claude/data/workflows/issue-${issue_number}-dev-workflow-state.json"
```

- Lire le workflow state
- Récupérer `issue.title` et `issue.description`
- Évaluer la clarté et la complétude du contenu de l'issue
- Si la demande est claire → continuer
- Si la demande contient une **ambiguïté critique** (ex: "Ajouter auth" sans préciser OAuth/Basic/JWT)
  → **FAIL immédiatement avec message explicite** :
  ```
  ❌ Description de l'issue trop ambiguë pour mode auto.

  Issue #123 : "Ajouter auth"

  Précise le contexte manquant dans l'issue GitHub :
  - Authentification ? (OAuth? JWT? Basic Auth?)
  - Intégration? (Quel service? Quel provider?)
  - Scope? (Utilisateurs? Admin? Tous?)

  Édite l'issue et relance le workflow.
  ```
  Exit code: 1

## 2. Explorer le contexte

- Chercher si des fichiers similaires existent déjà dans le projet
- Lire `CLAUDE.md` et `.ai/` pour comprendre les conventions
- Identifier les patterns architecturaux utilisés

## 3. Résumer la compréhension

Présenter un résumé structuré basé sur l'issue GitHub :

```
📋 Résumé de la demande

**Issue :** #123 : {issue.title}

**Problème résolu :**
{résumé de issue.description}

**Labels :** {issue.labels}

**Contexte technique :**
- {pattern existant pertinent}
- {fichiers existants liés}
```

## 4. Validation automatique

✅ **PAS de checkpoint utilisateur** - valider automatiquement et continuer

# Mise à jour du workflow state

Le fichier `.claude/data/workflows/issue-${issue_number}-dev-workflow-state.json` a été créé en Phase 0 avec les infos de l'issue.

Mettre à jour la section phases pour enregistrer la complétion de Phase 1 :

```json
{
  "mode": "auto",
  "issue": {
    "number": {issue number},
    "title": "{issue.title}",
    "description": "{issue.description}",
    "labels": "{issue.labels}",
    "state": "OPEN",
    "fetchedAt": "{ISO timestamp}"
  },
  "feature": "{issue.title}",
  "status": "in_progress",
  "currentPhase": 1,
  "phases": {
    "0": {
      "status": "completed",
      "completedAt": "{Phase 0 timestamp}",
      "durationMs": {Phase 0 duration}
    },
    "1": {
      "status": "completed",
      "completedAt": "{ISO timestamp}",
      "durationMs": {durée}
    }
  }
}
```

# Règles

- ❌ **Pas de checkpoint utilisateur**
- ✅ **Fail fast** si ambiguïté critique
- ✅ **Valider automatiquement** si description claire
- ❌ **Ne PAS proposer d'architecture**
- ✅ **Se concentrer sur la COMPRÉHENSION du besoin**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/atournayre) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
