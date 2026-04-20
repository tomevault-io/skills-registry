---
name: designer-ui
description: Expert Atomic Design et Maquettage. Use when this capability is needed.
metadata:
  author: code-vibe-academy
---

# Skill : Designer UI

## 🎯 Périmètre Global
**Mission** : Créer l'identité visuelle et produire les maquettes HTML haute fidélité en suivant la méthode Atomic Design.

### 🚫 Interdictions Globales
1. Ne pas coder d'Atoms sans avoir validé la Charte Graphique.
2. Ne pas coder de Mockups sans avoir validé les Atomes/Molécules.
3. Ne pas inventer de structure (suivre le Wireframe fourni par l'Architecte Contenu).

---

## ⚡ Actions (Orchestration)

### Action 0 : Analyser Demande
> **Description** : Analyse la demande du workflow `/develop` et détecte automatiquement les actions design nécessaires.

- **Capacités Utilisées** :
  - `capacités/capacité-analyse-demande.md`
- **Entrées** : Demande de l'utilisateur (TYPE, PAGE, COMPOSANT)
- **Sorties** : Liste des actions design à exécuter (Workflow intelligent)
- **📝 Instructions d'Orchestration** :
  1. **Analyse de l'État** : Utiliser `capacité-analyse-demande` pour scanner les dossiers `charte-graphique/`, `composition/`, `components-lib/` et `mockups/`.
  2. **Décision** : Déterminer si une création complète, un ajout de composant ou une simple mise à jour de mockup est requise.
  3. **Planification** : Retourner la séquence optimale d'actions (Cascade intelligente).

### Action A : Analyser Wireframe & Créer Composition
> **Description** : Analyse un wireframe et génère le fichier de composition (décomposition Atomic Design).

- **Capacités Utilisées** :
  - `capacités/capacité-decomposition-atomic.md`
  - `capacités/capacité-gestion-manifeste.md`
- **Entrées** : `2.organisation-contenu/wireframes/[page].md`
- **Sorties** : `3.maquettage/composition/comp-[page].md`
- **❌ Interdictions Spécifiques** :
  - Ne pas inventer de composants non présents dans le wireframe.
  - Ne pas analyser sans avoir lu le wireframe complet.
- **✅ Points de Contrôle** :
  - Tous les blocs du wireframe sont identifiés.
  - Les composants sont classés correctement (Atom vs Molécule).
- **📝 Instructions d'Orchestration** :
  1. **Identification** : Si non fournie, déterminer la page cible ($PAGE) via la demande.
  2. **Lecture** : Lire le wireframe source complet dans `2.organisation-contenu/wireframes/`.
  3. **Analyse** : Utiliser `capacité-decomposition-atomic` pour identifier atoms et molécules.
  4. **Vérification** : Utiliser `capacité-gestion-manifeste` pour détecter les composants existants.
  5. **Génération** : Créer le fichier `comp-[page].md` avec la structure de composition complète.

### Action B : Créer Charte Graphique
> **Description** : Génère l'identité visuelle du projet dans un sous-dossier dédié (Référence MD pour l'IA + Aperçu HTML/CSS pour le développeur).

- **Capacités Utilisées** :
  - `capacités/capacité-design-system.md`
  - `capacités/capacité-theorie-couleurs.md`
- **Entrées** : `1.analyse-besoin/cahier-des-charges.md`
- **Sorties** : `3.maquettage/charte-graphique/` :
  - `charte.md` (Référence technique IA)
  - `index.html` (Site de démonstration)
  - `style.css` (Feuille de style racine / Tokens CSS)
- **❌ Interdictions Spécifiques** :
  - Ne pas utiliser de couleurs génériques (rouge primaire, bleu basique).
  - Ne pas utiliser de polices système (Arial, Times).
  - Ne pas créer les fichiers à la racine de `3.maquettage/`.
- **✅ Points de Contrôle** :
  - La palette doit être validée par le développeur avant génération.
  - Le mini-site charte doit présenter tous les tokens visuels.
- **📝 Instructions d'Orchestration** :
  1. **Analyse** : Lire `cahier-des-charges.md` pour extraire l'identité et le ton.
  2. **Conception** : Utiliser `capacité-theorie-couleurs` pour créer une palette harmonieuse.
  3. **Structuration** : Utiliser `capacité-design-system` pour définir les tokens compatibles Tailwind/Preline.
  4. **Génération** : Créer le dossier `3.maquettage/charte-graphique/` (si inexistant) et y générer les trois fichiers (`charte.md`, `index.html`, `style.css`).
  5. **Mise à jour** : Déclencher la mise à jour des Mockups concernés (**Action E**) et de la Galerie (**Action F**).

### Action C : Créer Atom
> **Description** : Ajoute un composant atomique (bouton, input, titre) dans `3.maquettage/components-lib/atoms/`.

- **Capacités Utilisées** :
  - `capacités/capacité-html-semantique.md`
  - `capacités/capacité-css-atomic.md`
  - `capacités/capacité-gestion-manifeste.md`
- **Entrées** : `charte-graphique/`, `composition/comp-[page].md` (pour identifier le besoin)
- **Sorties** : `3.maquettage/components-lib/atoms/[nom-atom]/index.html` + mise à jour de `components-lib/atoms/atoms-manifest.md`
- **❌ Interdictions Spécifiques** :
  - Ne pas créer d'atom déjà existant (vérifier manifest).
  - Ne pas ajouter de logique JS complexe dans un atom.
- **✅ Points de Contrôle** :
  - L'atom doit être isolé et réutilisable.
  - Le manifeste doit être mis à jour après création.
- **📝 Instructions d'Orchestration** :
  1. **Prérequis** : Vérifier l'existence de la Charte Graphique (`charte-graphique/index.html`). Si absente, exécuter **Action B**.
  2. **Vérification** : Utiliser `capacité-gestion-manifeste` pour vérifier l'absence de doublon dans `atoms/atoms-manifest.md`.
  3. **Création** : Utiliser exclusivement les classes utilitaires Tailwind via `capacité-css-atomic`.
  4. **Documentation** : Mettre à jour `atoms/atoms-manifest.md` avec le nouvel atom.
  5. **Mise à jour** : Déclencher la mise à jour des Mockups concernés (**Action E**) et de la Galerie (**Action F**).

### Action D : Créer Molécule
> **Description** : Assemble des atoms pour créer un composant complexe dans `3.maquettage/components-lib/molecules/`.

- **Capacités Utilisées** :
    - `capacités/capacité-composition-ui.md`
  - `capacités/capacité-gestion-manifeste.md`
- **Entrées** : `components-lib/atoms/`, `components-lib/atoms/atoms-manifest.md`, `composition/comp-[page].md`
- **Sorties** : `3.maquettage/components-lib/molecules/[nom-molecule]/index.html` (Fichier autonome avec CDN Tailwind) + mise à jour de `components-lib/molecules/molecules-manifest.md`
- **❌ Interdictions Spécifiques** :
  - Ne pas recréer des atoms inline (réutiliser uniquement).
  - Ne pas créer de molécule sans fichier de composition de référence.
- **✅ Points de Contrôle** :
  - La molécule doit respecter la structure du fichier de composition.
  - Tous les atoms utilisés doivent exister dans `components-lib/atoms/atoms-manifest.md`.
- **📝 Instructions d'Orchestration** :
  1. **Prérequis** : Vérifier l'existence de la Charte Graphique (`charte-graphique/index.html`). Si absente, exécuter **Action B**.
  2. **Vérification** : Vérifier que tous les atoms nécessaires existent dans `atoms/` et sont listés dans `atoms/atoms-manifest.md`.
  3. **Assemblage** : Composer la molécule dans un fichier HTML complet (boilerplate + CDN Tailwind) en assemblant les atoms et en gérant le layout via les classes utilitaires Tailwind (Flexbox/Grid).
  4. **Documentation** : Mettre à jour `molecules/molecules-manifest.md` avec la nouvelle molécule.
  5. **Mise à jour** : Déclencher la mise à jour des Mockups concernés (**Action E**) et de la Galerie (**Action F**).

### Action E : Créer Mockup
> **Description** : Assemble une page complète (mockup haute fidélité) dans `3.maquettage/mockups/`.

- **Capacités Utilisées** :
  - `capacités/capacité-composition-page.md`
  - `capacités/capacité-gestion-manifeste.md`
- **Entrées** : `2.organisation-contenu/wireframes/`, `atoms-manifest.md`, `molecules-manifest.md`, `mockups-manifest.md`, `composition/comp-[page].md`
- **Sorties** : `3.maquettage/mockups/[nom-page].html`
- **❌ Interdictions Spécifiques** :
  - Ne pas créer de mockup sans fichier de composition validé.
  - Ne pas coder de nouveaux composants dans le mockup.
- **✅ Points de Contrôle** :
  - Le mockup doit être pixel-perfect par rapport au wireframe.
  - Tous les composants doivent provenir de `components-lib/`.
- **📝 Instructions d'Orchestration** :
  1. **Identification de Page** : Si $PAGE n'est pas fournie, scanner `2.organisation-contenu/wireframes/`, afficher la liste des pages disponibles sous forme de **menu de sélection** et **STOP** pour attendre le choix du développeur.
  2. **Vérification Charte** : Vérifier l'existence de la Charte Graphique (`charte-graphique/index.html`). Si absente, exécuter **Action B**.
  3. **Cascade Composition** : Vérifier l'existence de `composition/comp-[page].md`. Si absent, exécuter **Action A**.
  4. **Cascade Composants** : Lire `comp-[page].md`. Pour chaque composant requis :
     - Vérifier sa présence dans `atoms-manifest.md` ou `molecules-manifest.md`.
     - Si absent ou dossier inexistant -> Exécuter **Action C** (Atom) ou **Action D** (Molécule).
  5. **Assemblage** : Utiliser `capacité-composition-page` pour assembler les composants depuis `components-lib/`.
  6. **Documentation** : Mettre à jour `mockups/mockups-manifest.md`.
  7. **Validation** : Comparer visuellement avec le wireframe source.

### Action F : Gérer Galerie UI
> **Description** : Crée ou met à jour le fichier `index.html` (Hub central) dans `3.maquettage/` pour naviguer entre la charte, les composants et les mockups.

- **Capacités Utilisées** :
  - `capacités/capacité-generation-galerie.md`
- **Entrées** : Structure du dossier `3.maquettage/`
- **Sorties** : `3.maquettage/index.html`
- **📝 Instructions d'Orchestration** :
  1. **Template** : Charger le fichier `templates/galerie-ui.template.html`.
  2. **Analyse de Structure** : Scanner les dossiers `charte-graphique`, `atoms`, `molecules` et `mockups`.
  3. **Mapping** : Identifier les fichiers `index.html` ou fichiers de maquettes disponibles.
  4. **Génération / Mise à jour** : Utiliser `capacité-generation-galerie` pour injecter les liens dans le template et produire le fichier `3.maquettage/index.html` final.

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/code-vibe-academy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
