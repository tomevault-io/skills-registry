---
name: testing-matrix-navigator
description: Guide for selecting and executing the correct pytest suites (unit, integration, redis, R2, routing rules, magic link) with environment setup and coverage expectations. Use when this capability is needed.
metadata:
  author: ki2pixel
---

# Testing Matrix Navigator

Utilise cette compétence pour planifier et exécuter les tests pertinents après une modification.

## Pré-requis
- Virtualenv `/mnt/venv_ext4/venv_render_signal_server` activé.
- Variables ENV exportées (`FLASK_SECRET_KEY`, etc.) via `.env`.
- Accès aux marqueurs Pytest réellement déclarés (`unit`, `integration`, `e2e`, `slow`, `redis`, `imap`).

## Matrice de décision
| Contexte | Commande helper | Description |
| --- | --- | --- |
| Modifications backend générales | `./run_tests.sh -u` | Tests unitaires sans dépendances externes |
| Config/Redis | `pytest -m redis` | Tests marqués Redis |
| R2 / Offload | `pytest tests/test_r2_transfer_service.py` | Couverture ciblée du service R2 |
| Routing rules | `pytest tests/routes/test_api_routing_rules.py tests/email_processing/test_routing_rules_orchestrator.py` | API + orchestrateur |
| Magic link | `pytest tests/test_services.py -k magic_link` | Couverture ciblée du service Magic Link |
| Full suite | `./run_tests.sh -a -c` | Tous les tests avec couverture |

## Workflow
1. **Préparer l'environnement**
   - Activer `/mnt/venv_ext4/venv_render_signal_server`.
   - Exporter les variables requises via `.env`.
2. **Sélectionner la suite**
   - Utiliser `./run_tests.sh` avec les options appropriées.
   - Pour un diff large, exécuter `pytest --maxfail=1 --disable-warnings` d'abord, puis `pytest --cov=.`.
3. **Commandes avancées**
   - Tests ciblés : `pytest tests/routes/test_api_routing_rules.py`
   - Scénarios spécifiques : `pytest tests/email_processing/test_routing_rules_orchestrator.py -k stop_processing`
   - Magic link : `pytest tests/test_services.py -k magic_link`
   - R2 : `pytest tests/test_r2_transfer_service.py`
   - Durées : `pytest --durations=20`
4. **Analyse des résultats**
   - Corriger immédiatement les échecs introduits.
   - Si une suite est flaky, documenter dans la Memory Bank avec étapes de reproduction.
5. **Rapports**
   - Pour la CI, viser couverture ≥70% (88 cols, black/isort conformes).

## Ressources
- `run_tests.sh` : script principal à la racine qui active le venv et exécute les suites demandées (unit, integration, e2e, fast, coverage).

## Conseils
- Grouper les tests par dossier modifié pour diagnostiquer plus vite.
- Relire `pytest.ini` pour les options par défaut (plugins, markers).
- Ajouter des tests Given/When/Then lors de nouvelles fonctionnalités.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ki2pixel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
