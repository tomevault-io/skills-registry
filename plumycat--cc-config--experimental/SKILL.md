---
name: experimental
description: Gestion des branches expérimentales pour tester les nouvelles features Claude Code Use when this capability is needed.
metadata:
  author: plumycat
---

# Workflow de test des nouveautés

Gère les branches expérimentales pour tester les nouvelles features Claude Code avant de les adopter.

## Commandes disponibles

### Démarrer un test
```bash
~/cc-config/scripts/experimental.sh start $ARGUMENTS
```
- Crée une branche `exp/<nom-feature>`
- Permet de modifier la config en isolation

### Voir le statut
```bash
~/cc-config/scripts/experimental.sh status
```
- Affiche la branche courante
- Montre les modifications en cours

### Valider et merger
```bash
~/cc-config/scripts/experimental.sh validate
```
- Merge la branche expérimentale dans main
- Supprime la branche exp/*

### Annuler le test
```bash
~/cc-config/scripts/experimental.sh rollback
```
- Abandonne les modifications
- Revient sur main

### Historique
```bash
~/cc-config/scripts/experimental.sh list
```
- Liste les tests précédents

## Workflow typique

1. `/experimental start nouvelle-feature`
2. Modifier la config, tester
3. `/experimental validate` si OK, ou `/experimental rollback` si KO

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plumycat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
