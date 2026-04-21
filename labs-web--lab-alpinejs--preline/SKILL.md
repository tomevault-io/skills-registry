---
name: preline-ui-expert
description: Guide pour le développement d'interfaces CRUD (Admin & Public) avec Preline UI et Tailwind CSS. Use when this capability is needed.
metadata:
  author: labs-web
---

# Preline UI - Guide de Développement

## Vue d'Ensemble
Ce skill permet de générer des interfaces utilisateur modernes, responsives et accessibles en utilisant la bibliothèque [Preline UI](https://preline.co/). Il est spécialisé dans deux contextes :
1.  **Back-Office / Admin** : Tableaux de données, formulaires, sidebars, modales.
2.  **Front-Office / Public** : Landing pages, grilles d'articles, headers, footers.

## Installation et Configuration
Lors de l'initialisation d'un projet ou d'une demande d'intégration :

1.  **Tailwind Config** : Vérifier que le plugin Preline est bien présent dans `tailwind.config.js`.
    ```javascript
    module.exports = {
      content: [
        './node_modules/preline/dist/*.js',
        // ... autres chemins
      ],
      plugins: [
        require('preline/plugin'),
      ],
    }
    ```
2.  **JavaScript** : Importer Preline dans `resources/js/app.js` (ou équivalent).
    ```javascript
    import 'preline';
    ```

## Modèles de Composants

### 1. Structure Admin (Layout)
Utiliser le pattern **Sidebar Layout** :
-   **Sidebar** fixe à gauche (desktop) / Drawer (mobile).
-   **Header** avec profil utilisateur et toggle sidebar.
-   **Main Content** pour le CRUD.

### 2. Tableaux CRUD (Data Tables)
Pour les listes (ex: Articles, Utilisateurs), utiliser le style "Card Table" de Preline :
-   Conteneur avec ombre légère et bords arrondis.
-   En-tête de table avec fond gris clair (`bg-gray-50`).
-   Actions (Editer/Supprimer) groupées ou dans un dropdown.
-   Pagination en bas de carte.

Exemple de classes clés :
-   Table: `min-w-full divide-y divide-gray-200 dark:divide-gray-700`
-   Header Th: `px-6 py-3 text-start text-xs font-medium text-gray-500 uppercase`

### 3. Modales & Overlays
Pour les formulaires de création/édition :
-   Utiliser les modales Preline (`hs-overlay`).
-   S'assurer que les modales sont accessibles (focus trap géré par Preline).

### 4. Composants Publics
-   **Hero Sections** : Titres impactants, CTA clairs.
-   **Cards** : Pour les blogs/articles, utiliser des cartes avec image, tag, titre et extrait.
-   **Features** : Grilles d'icônes pour présenter les fonctionnalités.

## Intégration avec Alpine.js
Bien que Preline possède son propre JS, il cohabite bien avec Alpine.
-   **Préférence** : Pour la logique purement UI (toggle, dropdown), on peut utiliser les attributs data de Preline (`data-hs-overlay`).
-   **Logique Métier** : Pour la gestion d'état (formulaire, fetch data), utiliser Alpine (`x-data`).
-   **Conflits** : Éviter de contrôler la *même* visibilité avec `x-show` ET les classes Preline `hs-overlay-open` simultanément sans précaution.

## Prompting
Pour demander un composant, spécifier le type (Admin vs Public) et les données à afficher.
Exemple : "Génère une table CRUD pour les produits avec Preline, incluant image, nom, et stock."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/labs-web) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
