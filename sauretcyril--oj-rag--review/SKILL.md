---
name: review
description: Revoir le code pour la qualité, sécurité et bonnes pratiques Use when this capability is needed.
metadata:
  author: sauretcyril
---

# Skill de revue de code

## Étapes de revue

1. **Analyser les changements**
   - Identifier les fichiers modifiés
   - Comprendre l'objectif des modifications

2. **Vérifier la qualité du code**
   - Lisibilité et maintenabilité
   - Respect des conventions du projet
   - Pas de code dupliqué

3. **Contrôles de sécurité**
   - Pas d'injection SQL (utiliser TypeORM correctement)
   - Pas de XSS (échapper les données utilisateur)
   - Pas de secrets en dur dans le code
   - Validation des entrées utilisateur

4. **Performance**
   - Requêtes N+1 évitées
   - Pas de boucles inutiles
   - Utilisation correcte des hooks React (useMemo, useCallback)

5. **Tests**
   - Les fonctionnalités critiques sont testées
   - Les cas limites sont couverts

## Format du feedback

### Critique (à corriger)
- Problèmes de sécurité
- Bugs évidents

### Avertissement (à améliorer)
- Code difficile à maintenir
- Performances sous-optimales

### Suggestion (à considérer)
- Améliorations optionnelles
- Refactoring possible

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sauretcyril) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
