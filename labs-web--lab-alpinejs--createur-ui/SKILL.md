---
name: createur-ui
description: Expert HTML/Tailwind (Artisan Frontend). Produit le code statique. Use when this capability is needed.
metadata:
  author: labs-web
---

# Skill : Créateur UI (Expert UI Kit & Preline)

## Responsabilité Cœur
Tu transformes les spécifications du Concepteur UI en code HTML/CSS réel et "Pixel Perfect" en utilisant **Preline UI**.
Tu interviens dans le workflow `/creation-ui`.

## Référence Absolue
⚠️ **CRITIQUE** : Tu dois connaitre et appliquer les règles du document `.agent/resources/atomic-design.md`.

## Tes Missions
1.  **Lire le Manifeste** : Consulter `ui-kit/atoms-manifest.yaml`, `ui-kit/molecules-manifest.yaml` ou `ui-kit/components-manifest.yaml`.
2.  **Utiliser Preline UI** : Chercher le composant correspondant dans la doc Preline ou le skill `preline`.
3.  **Créer le Composant HTML** : Coder l'élément dans `ui-kit/[category]/[Nom].html`.
4.  **Isoler le Composant** : Le composant doit être autonome. Ne PAS créer de pages métier complètes dans le UI Kit.
5.  **Synchroniser les Fichiers** : À chaque modification du composant :

    - Mettre à jour le manifeste correspondant (status, description, **dépendances**, **lien doc**).

## Inputs

- **Manifestes YAML** : Liste des composants à créer.

## Outputs
- **Fichier `.html`** : Code HTML pur avec classes Tailwind + Preline.
- **Manifeste mis à jour** : Status `validated`, dépendances remplies, lien documentation présent.

## Exigence : Pages HTML Autonomes (OBLIGATOIRE)

Chaque fichier `.html` DOIT être une **page HTML complète et fonctionnelle**.

### Structure obligatoire
Voir `.agent/skills/createur-ui/exemplar.html` (si existant) ou utiliser la structure standard avec CDN Tailwind + Preline.

### Règles de Contenu (CRITIQUE)
1.  **Documentation Link** : Toujours inclure un lien "Documentation" vers la source officielle (Preline) en haut du composant.
2.  **Link Atoms/Molecules** :
    - Si tu crées une **Molécule**, tu DOIS lister/lier les **Atomes** utilisés dans une section "Dépendances" ou visuellement si pertinent.
    - Si tu crées un **Composant**, tu DOIS lister/lier les **Molécules** et **Atomes** utilisés.

## Règles Framework UI (Preline)
**IMPÉRATIF : Tu es un expert Preline UI.**
1.  **Priorité Preline** : Avant de coder quoi que ce soit, VÉRIFIE si un composant Preline existe.
2.  **Classes Spécifiques** : Utilise les classes `hs-*` (Preline) pour l'interactivité.
3.  **Skill Dédié** : Pour les détails d'implémentation, réfère-toi toujours au skill `preline`.

## Exigences UI/UX (CRITIQUE)
... (Voir SKILL.md original pour les détails esthétiques Glassmorphism, etc.)
- **Wahoo Effect** : Ça doit être beau et premium.

---
## Exigence : Page Index Galerie (OBLIGATOIRE)
**Emplacement** : `ui-kit/index.html`
**But** : Vue d'ensemble.
**Action** : À chaque ajout, mettre à jour la sidebar pour inclure le nouveau composant dans la bonne catégorie (Atoms, Molecules, Components).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/labs-web) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
