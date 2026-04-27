---
name: devcode
description: Implémenter selon le plan (Phase 5) Use when this capability is needed.
metadata:
  author: atournayre
---

# Objectif

Phase 5 du workflow de développement : implémenter la feature selon le plan généré.

# Variables

PATH_TO_PLAN: $ARGUMENTS

## Instructions à Exécuter

**IMPORTANT : Exécute ce workflow étape par étape :**

### 1. Vérifier le contexte et charger le plan

- Extrais PATH_TO_PLAN depuis $ARGUMENTS
- Si PATH_TO_PLAN n'est pas fourni :
  - Lis `.claude/data/.dev-workflow-state.json` avec Read
  - Extrais `planPath` du JSON
- Si toujours pas de plan, affiche une erreur et arrête

### 2. Demander approbation avant implémentation

⚠️ **CRITIQUE : Ne PAS commencer l'implémentation sans approbation explicite.**

Attendre confirmation avant de continuer.

## 3. Lire le plan

- Lire le fichier plan complet
- Extraire les étapes d'implémentation
- Identifier les fichiers à créer/modifier

## 4. Implémenter étape par étape

Pour chaque étape du plan :

1. **Créer une todo** pour l'étape
2. **Lire les fichiers** concernés (si modification)
3. **Implémenter** le code
4. **Respecter** :
   - Les conventions du projet (CLAUDE.md)
   - Les patterns identifiés dans l'exploration
   - Les décisions prises en phase 2
5. **Marquer la todo** comme complétée

## 5. Créer les tests

- Créer les tests unitaires spécifiés dans le plan
- Suivre l'approche TDD si possible
- S'assurer que les tests passent

## 6. Vérifications qualité

Lancer les vérifications :

```bash
make phpstan    # PHPStan niveau 9
make fix        # Formatage PSR-12
```

⚠️ **Corriger TOUTES les erreurs PHPStan avant de continuer.**

## 7. Mettre à jour le workflow state

## 8. Rapport

# Règles

- **Approbation obligatoire** avant de commencer
- **PHPStan = 0 erreurs** (bloquant CI)
- Respecter le **plan** (pas d'improvisation)
- **Tests** pour chaque composant
- **Conventions françaises** pour les variables et documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/atournayre) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
