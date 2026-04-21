---
name: routing-rules-orchestrator
description: Streamline any change touching the dynamic routing rules stack (service, API, orchestrator, frontend) with mandatory validation steps and test coverage. Use when this capability is needed.
metadata:
  author: ki2pixel
---

# Routing Rules Orchestrator

Utilise ce skill pour modifier ou étendre le moteur de routage dynamique introduit en janvier 2026.

## Pré-requis
- Virtualenv `/mnt/venv_ext4/venv_render_signal_server` pour les tests.
- Accès au dashboard pour les tests manuels UI.
- Connaissance des types existants (`RoutingRuleCondition`, `RoutingRuleAction`, `RoutingRule`).

## Workflow
1. **Cartographier l'impact**
   - Identifier les couches concernées : `services/routing_rules_service.py`, `routes/api_routing_rules.py`, `email_processing/orchestrator.py`, `static/services/RoutingRulesService.js`, `dashboard.html`, tests associés.
2. **Mettre à jour le schéma**
   - Réutiliser les constantes/types existants.
   - Respecter la validation stricte (opérateurs autorisés, normalisation strings, booléens explicites).
3. **Propager côté API**
   - Ajouter les champs dans la validation custom portée par `RoutingRulesService.update_rules()`.
   - Couvrir les erreurs 400 détaillées.
4. **Adapter l'orchestrateur**
   - Étendre `_match_routing_condition` ou `_find_matching_routing_rule` sans casser les early returns.
   - Logger via `app_logging` (pas d'info sensible).
5. **MAJ Frontend**
   - Builder ES6 : manipuler `routingRules` via fonctions pures, pas de `innerHTML`.
   - Ajouter les collectors et états UI (saving/saved/error) avec `MessageHelper`.
6. **Tests & validation**
   - Lancer le helper `bash ./.windsurf/skills/routing-rules-orchestrator/test_routing_rules.sh`.
   - Compléter si besoin avec des tests ciblés sur les nouvelles fonctionnalités.
7. **Documentation**
   - Documenter toute nouvelle action/condition dans `docs/processing/routing-engine.md`.
   - Mettre à jour la Memory Bank si des décisions architecturales sont prises.

## Ressources
- `test_routing_rules.sh` : active le venv, exécute les suites service, API, orchestrator et les scénarios stop_processing.

## Conseils
- Conserver la compatibilité avec les règles legacy (`stop_processing`, fallback backend).
- Ajouter des migrations de données (script ou instructions) si le schéma change.
- Tester manuellement via le dashboard si des changements UI sont introduits.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ki2pixel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
