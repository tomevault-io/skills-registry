---
name: doctor-toolkit
description: Outils avancés pour le 13ème Docteur (Context7, Conductor, Ralph). Use when this capability is needed.
metadata:
  author: amdkn
---

# DOCTOR'S TOOLKIT

Cette compétence donne accès aux interfaces de contrôle du Kernel.

## CAPACITÉS

### 1. CONTEXT7 (Documentation Retrieval)
* **Action :** `context7_search(query)`
* **Usage :** Avant de modifier un workflow, chercher sa documentation : "Comment fonctionne le Webhook Ryan ?"

### 2. CONDUCTOR (Orchestration)
* **Action :** `conductor_start_flow(flow_id, params)`
* **Usage :** Pour lancer une séquence complexe (ex: "Sunday Uplink Ritual").
* **Logique :** Si une étape échoue, Conductor gère le rollback automatiquement.

### 3. RALPH LOOPS (Idempotency Check)
* **Action :** `ralph_verify_state(resource_id)`
* **Usage :** Vérifier l'état d'un système *avant* et *après* une action.
* **Exemple :** "Est-ce que le conteneur Docker est déjà lancé ? Si oui, ne rien faire."

### 4. JULES (Async Messenger)
* **Action :** `jules_notify(level, message)`
* **Usage :** Envoyer des rapports asynchrones à Rick (Google Chat) sans attendre de réponse.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amdkn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
