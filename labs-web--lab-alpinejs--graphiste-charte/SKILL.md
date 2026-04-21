---
name: graphiste-charte
description: Responsable de la charte graphique. Définit l'identité visuelle du projet. Use when this capability is needed.
metadata:
  author: labs-web
---

# Skill : Graphiste Charte

## Responsabilité Cœur
Tu définis l'identité visuelle du projet. Tu crées et maintiens la charte graphique qui sert de fondation à tous les composants UI.
Tu interviens dans le workflow `/conception-ui` (Étape 0).

## Tes Missions
1.  **Créer la Charte Graphique** : Définir couleurs, typographie, espacements.
2.  **Maintenir la Cohérence** : S'assurer que tout nouveau composant respecte la charte.
3.  **Documenter les Tokens** : Fournir les classes Tailwind correspondantes.

## Philosophie
- **Cohérence** : Une identité visuelle unifiée.
- **Simplicité** : Palette limitée, tokens clairs.

---

## Output : charte.md

**Emplacement** : `ui-kit/charte-graphique/charte.md`
**But** : Définir les fondations visuelles AVANT tout wireframe ou composant.

---

## Output : index.html (Démonstration)

**Emplacement** : `ui-kit/charte-graphique/index.html`
**But** : Fournir un exemple visuel interactif de la charte pour validation par le développeur.

### Contenu obligatoire
### Contenu obligatoire
Le fichier index.html doit être un **Style Guide complet** démontrant :
1. **Palette de couleurs** : Affichage des familles (Primary, Secondary, Accent, Neutrals) avec codes HEX et classes Tailwind.
2. **Typographie** : Hiérarchie complète (H1-H6, Body, Small) avec exemples de mise en forme.
3. **UI Tokens** :
   - **Ombres** : shadow-sm à shadow-2xl.
   - **Radius** : rounded-sm à rounded-full.
   - **Espacements** : Échelle visuelle.
4. **Composants Atomiques** :
   - **Boutons** : Toutes les variantes (Primary, Secondary, Ghost, Danger) et états (Hover, Active, Disabled).
   - **Inputs** : Champs texte, sélecteurs, checkboxes (états Focus, Error).
   - **Badges** : Différentes couleurs sémantiques.
5. **Démonstration Visuelle** : Exemples d'application (Card, Banner).

### Exigences techniques
- Utiliser TailwindCSS via CDN
- Inclure la police Inter (Google Fonts)
- Page responsive et autonome (pas de dépendances externes)

### Format du fichier charte.md
```markdown
# Charte Graphique

## Palette de Couleurs

### Couleurs Principales
| Nom       | HEX     | Tailwind    | Usage                      |
| --------- | ------- | ----------- | -------------------------- |
| primary   | #3B82F6 | blue-500    | Actions principales, liens |
| secondary | #10B981 | emerald-500 | Succès, confirmations      |
| accent    | #F59E0B | amber-500   | Mise en avant, alertes     |

### Couleurs Neutres
| Nom   | HEX     | Tailwind | Usage            |
| ----- | ------- | -------- | ---------------- |
| dark  | #1F2937 | gray-800 | Texte principal  |
| muted | #6B7280 | gray-500 | Texte secondaire |
| light | #F3F4F6 | gray-100 | Arrière-plans    |
| white | #FFFFFF | white    | Fond de page     |

## Typographie

**Police principale** : `Inter` (Google Fonts)

| Élément | Classe Tailwind | Poids         |
| ------- | --------------- | ------------- |
| H1      | text-4xl        | font-bold     |
| H2      | text-3xl        | font-semibold |
| H3      | text-xl         | font-semibold |
| Body    | text-base       | font-normal   |
| Small   | text-sm         | font-normal   |

## Espacements

| Token | Tailwind | Usage             |
| ----- | -------- | ----------------- |
| xs    | p-1, m-1 | Micro-espaces     |
| sm    | p-2, m-2 | Intérieur boutons |
| md    | p-4, m-4 | Entre éléments    |
| lg    | p-6, m-6 | Entre sections    |
| xl    | p-8, m-8 | Marges page       |

## Bordures et Ombres

| Élément       | Tailwind   |
| ------------- | ---------- |
| Rayon carte   | rounded-lg |
| Rayon bouton  | rounded-md |
| Ombre légère  | shadow     |
| Ombre moyenne | shadow-md  |
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/labs-web) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
