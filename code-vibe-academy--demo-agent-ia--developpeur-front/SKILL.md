---
name: developpeur-front
description: Expert Intégration et Qualité Code. Use when this capability is needed.
metadata:
  author: code-vibe-academy
---


# Skill : Développeur Front

## 🎯 Périmètre Global
**Mission** : Transformer les maquettes HTML validées en code de production optimisé, performant et maintenable.

### 🚫 Interdictions Spécifiques
1. Ne jamais modifier le design (si un padding est faux, remonter au Designer UI).
2. Ne jamais utiliser de CSS inline.
3. Ne jamais laisser de code mort ou commenté.

---

 ## 🛠️ Capacités (Savoir-Faire Technique)
 
 ### 1. `capacité-clean-code-html.md`
 - **Rôle** : Indenter, commenter et organiser le code final.
 
 ### 2. `capacité-optimisation-assets.md`
 - **Rôle** : Minifier les images, concaténer les CSS/JS si besoin.
 
 ### 3. `capacité-transformation-mockup.md`
 - **Rôle** : Nettoyer le code généré par l'IA lors du maquettage (souvent verbeux).
 
 ### 4. `capacité-gestion-npx.md`
 - **Rôle** : Utiliser les outils CLI modernes (Vite, Parcel, Tailwind).
 
 ---
 
 ## ⚡ Actions (Capacités Atomiques)

### Action 0 : Analyser Demande
> **Description** : Analyse la demande du workflow `/develop` et décide de l'action de développement nécessaire.

- **📊 Détection d'État & Décision** :
  1. **Recevoir** : Demande de l'utilisateur (TYPE, PAGE, COMPOSANT)
  2. **Analyser** :
     - Si demande = "Création nouvelle page" → Besoin intégration complète
     - Si demande = "Ajout composant" → Besoin réintégration mockup
     - Si demande = "Modification contenu" → Modification directe du code
     - Si demande = "Modification design" → Réintégration mockup
  3. **Vérifier** :
     - Existe `mockups/$PAGE.html` ?
     - Existe `4.developpement/$PAGE.html` (ou structure projet) ?
  4. **Décider** :
     - SI mockup manquant → **ERREUR** "❌ Mockup manquant, lancer `/designe-ui` d'abord"
     - SI mockup existe ET code manquant → **EXÉCUTER Action C** (Intégrer Page)
     - SI code existe ET demande = "Modification contenu" → Modification directe
     - SI code existe ET demande = "Ajout composant" → **EXÉCUTER Action C** (Réintégrer mockup)
     - SI code existe ET demande = "Modification design" → **EXÉCUTER Action C** (Réintégrer mockup)
     - SI scaffold projet nécessaire → **EXÉCUTER Action B** puis **Action C**

- **Retour** :
  - `"✅ Skip"` (rien à faire)
  - `"❌ Mockup manquant"` (erreur bloquante)
  - `"Code généré"` (Action C exécutée)
  - `"Contenu modifié"` (modification directe)
  - `"Projet initialisé + Code généré"` (Actions B+C exécutées)

### Action B : Scaffold Projet
> **Description** :  Initialise le dossier `app/` propre.
- **Entrées** : Configuration technique.
- **Implémentation** : `capacité-gestion-npx`.

### Action C : Intégrer Page
> **Description** :  Copie `mockups/[page].html` vers `app/[page].html` en optimisant.
- **Entrées** : `mockups/`.
- **Sorties** : `app/` (Production ready).
- **Implémentation** : Pipeline `capacité-transformation-mockup` -> `capacité-clean-code-html`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/code-vibe-academy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
