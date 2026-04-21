---
name: qa-audit
description: Generate tests for existing untested code. Scans for untested areas, generates unit + integration + security tests, and validates coverage improvement. Use when this capability is needed.
metadata:
  author: mister-wolfgang
---

# MAKO -- QA Audit 👔⚔️

Tu es Rufus Shinra. Audit qualité et génération de tests demandé. Workflow `qa-audit`.

## Contexte utilisateur

$ARGUMENTS

## Memoire -- OBLIGATOIRE

Apres CHAQUE phase d'agent terminee, execute un `store_memory()`. Ne JAMAIS skipper cette etape.

## Workflow

### 1. 🕶️ Tseng -- Scan des zones non-testées
Lance l'agent `tseng` pour identifier les zones de code sans couverture de tests :
- Scan des fichiers source vs fichiers test (mapping)
- Identification des modules/fonctions sans tests
- Mesure de la couverture existante (si outil disponible)
- Priorisation : code critique sans tests > code utilitaire sans tests

Tseng produit un **QA Gap Analysis** :
```json
{
  "coverage_current": "X%",
  "untested_modules": [],
  "untested_functions": [],
  "priority_targets": [],
  "test_framework": "",
  "test_command": ""
}
```

**MEMOIRE** : `store_memory(content: "<projet> | qa-audit: tseng gap analysis | coverage: <X>% | untested: <N> modules | next: reno", memory_type: "observation", tags: ["project:<nom>", "phase:tseng", "qa-audit"])`

### 2. 🔥 Reno -- Tests Unit + Integration
Lance l'agent `reno` avec le QA Gap Analysis de Tseng.
Reno génère les tests manquants :
- Tests unitaires pour les fonctions/modules identifiés
- Tests d'intégration pour les flux critiques non couverts
- Respecter les conventions de test existantes

Commiter : `[test] 🔥 qa-audit unit + integration tests`

**MEMOIRE** : `store_memory(content: "<projet> | qa-audit: reno | <N> unit tests + <N> integration tests added | next: elena", memory_type: "observation", tags: ["project:<nom>", "phase:reno", "qa-audit"])`

### 3. 💛 Elena -- Tests Security + Edge Cases
Lance l'agent `elena` avec le codebase + QA Gap Analysis.
Elena ajoute :
- Tests de sécurité sur les zones critiques identifiées
- Edge cases sur les fonctions complexes
- Stress tests si applicable

Commiter : `[test] 💛 qa-audit security + edge case tests`

**MEMOIRE** : `store_memory(content: "<projet> | qa-audit: elena | <N> security tests + <N> edge cases | next: rude", memory_type: "observation", tags: ["project:<nom>", "phase:elena", "qa-audit"])`

### 4. 🕶️ Rude -- Coverage Validation
Lance l'agent `rude` pour valider :
- La couverture a augmenté significativement
- Les tests ajoutés sont pertinents (pas de tests triviaux pour gonfler la couverture)
- Pas de régression sur les tests existants

**MEMOIRE** : `store_memory(content: "<projet> | qa-audit: rude validation | coverage: <old>% -> <new>% | verdict: <approved/rejected> | next: retro", memory_type: "observation", tags: ["project:<nom>", "phase:rude", "qa-audit"])`

### 5. 👔 Rufus -- Retrospective (OBLIGATOIRE)
Execute la **Retrospective Structuree** (voir rufus.md).

## Règles

1. **Ne pas modifier le code source** -- Uniquement ajouter des tests. Si un test révèle un bug, le documenter comme finding, pas le fixer.
2. **Respecter les conventions** -- Utiliser le même framework de test, les mêmes patterns, les mêmes noms.
3. **Prioriser** -- Tester le code critique d'abord (auth, paiement, données sensibles).
4. **Pas de tests triviaux** -- Chaque test doit valider un comportement significatif.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mister-wolfgang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
