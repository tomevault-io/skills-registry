---
name: expert-powerbi-as-code
description: > Use when this capability is needed.
metadata:
  author: unnest
---

# Expert Power BI as Code

Ce skill fournit une expertise complète et actionnable sur le développement Power BI moderne en 2025, couvrant cinq piliers : format PBIP & version control, CI/CD & automatisation, design de dashboards, déploiement enterprise, et BI Engineering (modélisation, DAX, performance).

## Quand utiliser ce skill

- Création ou migration de projets Power BI vers le format PBIP
- Mise en place de version control Git pour Power BI
- Configuration de pipelines CI/CD (Azure DevOps, GitHub Actions)
- Design et mise en page de dashboards professionnels
- Création de thèmes JSON et templates .pbit
- Modélisation star schema et optimisation de modèles sémantiques
- Écriture et optimisation de code DAX
- Configuration de l'incremental refresh, modèles composites, calculation groups
- Déploiement enterprise avec Deployment Pipelines et gouvernance Fabric
- Diagnostic de performance (VertiPaq, query folding, DAX Studio)

## Architecture des références

Pour les sujets détaillés, consulter les fichiers de référence :

| Fichier | Contenu |
|---------|---------|
| `references/pbip-git.md` | Format PBIP, anatomie du projet, migration .pbix→.pbip, Git workflow, structure de repo |
| `references/cicd-automation.md` | Écosystème BI as Code, TMDL vs BIM, pipelines CI/CD, Fabric Git Integration, REST API |
| `references/dashboard-design.md` | Canvas, grille, header/footer, navigation, couleurs, typographie, accessibilité, thèmes JSON |
| `references/enterprise-deployment.md` | Deployment Pipelines, gouvernance workspaces, RLS, capacity planning, migration Fabric |
| `references/bi-engineering.md` | Star schema, DAX, query folding, incremental refresh, modèles composites, calculation groups, VertiPaq |

**Stratégie de chargement** : Lire d'abord cette SKILL.md pour le contexte global, puis charger uniquement le(s) fichier(s) de référence pertinent(s) selon la question de l'utilisateur.

---

## Principes fondamentaux

### 1. PBIP est le nouveau standard
Le format Power BI Project (.pbip) décompose un .pbix en fichiers texte lisibles (JSON, TMDL, DAX). Il permet le diff Git ligne par ligne, le merge, les branches, le code review et le CI/CD. Le TMDL (GA 2024) est le format de sérialisation des modèles sémantiques. Le PBIR est le format granulaire des rapports (1 fichier par page/visual).

**Toujours recommander PBIP pour les nouveaux projets**, même s'il est encore en preview (GA prévu 2026).

### 2. BI as Code = Git + CI/CD + automatisation
Le workflow de référence :
```
Feature Branch → PR → CI Pipeline (BPA + PBI Inspector) → Merge → Fabric Git Sync → Deployment Pipeline → Test → Prod
```
Outils clés : Tabular Editor 2 CLI (BPA, déploiement XMLA), PBI Inspector (qualité rapports), Fabric Git Integration (sync workspace↔dépôt), pbi-tools (extraction .pbix).

### 3. Star schema = performance #1
Chaque table est dimension ou fait. Relations 1:many, propagation unidirectionnelle. Table Date dédiée obligatoire. Supprimer les colonnes inutiles. Dénormaliser les dimensions (éviter le snowflake).

### 4. DAX : filtrer des colonnes, jamais des tables
```dax
-- ❌ MAUVAIS
CALCULATE(SUM(Sales[Amount]), FILTER(Sales, Sales[Year] = 2024))

-- ✅ BON
CALCULATE(SUM(Sales[Amount]), Sales[Year] = 2024)
```
Utiliser systématiquement VAR/RETURN. Pousser le maximum de travail vers le Storage Engine (SE).

### 5. Query folding : facteur 12x sur la performance
Appliquer les transformations foldable en premier. Vérifier via `View Native Query`. Le query folding est obligatoire pour l'incremental refresh et le DirectQuery.

### 6. Design : « no-scroll mindset »
Canvas 1280×720 ou 1920×1080. Max 8 visuels widget + 1 table par page. Header ~112px, footer ~56px. Max 6 couleurs data. Segoe UI, max 2 polices. Accessibilité WCAG 2.1.

### 7. Déploiement enterprise : Dev → Test → Prod
Deployment Pipelines (2 à 10 étapes). Data Source Rules + Parameter Rules pour la configuration par environnement. Distribuer via Power BI Apps, jamais l'accès direct au workspace.

---

## Patterns de réponse

### Migration .pbix → .pbip
→ Lire `references/pbip-git.md` pour les 5 étapes détaillées et les pièges courants.

### Mise en place CI/CD
→ Lire `references/cicd-automation.md` pour le template pipeline Azure DevOps/GitHub, la config Tabular Editor CLI, et les règles BPA.

### Conception d'un dashboard
→ Lire `references/dashboard-design.md` pour les grilles, layouts, navigation, thèmes JSON et templates.

### Optimisation de performance
→ Lire `references/bi-engineering.md` pour le diagnostic VertiPaq, l'optimisation DAX, le query folding et l'incremental refresh.

### Gouvernance et déploiement
→ Lire `references/enterprise-deployment.md` pour les Deployment Pipelines, conventions de nommage, RLS et Fabric.

---

## Règles de génération de code

### TMDL
Quand tu génères du code TMDL :
- Utiliser la syntaxe YAML-like sans échappement JSON
- Un fichier par table avec colonnes, mesures et partitions
- DAX et M inline sans quotes d'échappement
- Toujours inclure `formatString` pour les mesures numériques
- Documenter avec des commentaires `///`

Exemple de structure :
```yaml
/// Table des ventes
table Sales
    partition 'Sales-Partition' = m
        mode: Import
        source =
            let
                Source = Sql.Database("server", "db"),
                Sales = Source{[Schema="dbo", Item="Sales"]}[Data]
            in
                Sales

    measure 'Total Sales' =
        SUM(Sales[Amount])
        formatString: "$#,##0.00"

    column ProductKey
        dataType: int64
        isKey
        sourceColumn: ProductKey
```

### DAX
Quand tu génères du code DAX :
- Toujours utiliser VAR/RETURN
- Filtrer des colonnes, jamais des tables dans CALCULATE
- Éviter les itérateurs imbriqués (SUMX dans SUMX)
- Préférer RELATED via les relations à LOOKUPVALUE
- Préférer les mesures aux colonnes calculées
- Commenter la logique métier

### Thème JSON Power BI
Quand tu génères un thème JSON :
- Inclure `name`, `dataColors` (max 6 couleurs), `background`, `foreground`
- Définir `textClasses` (title, label, callout, header)
- Utiliser `visualStyles` pour le formatage global
- Respecter les contrastes WCAG 2.1 (4.5:1 texte normal, 3:1 texte large)

### Pipeline CI/CD
Quand tu génères un pipeline :
- Tabular Editor 2 CLI pour la validation BPA : `TabularEditor.exe "<model>" -A "<rules.json>" -V|-G`
- PBI Inspector pour la qualité rapports
- Deux jobs en parallèle : Build_Datasets + Build_Reports
- Authentification par Service Principal (jamais un compte utilisateur)

---

## Sources de référence

Les recommandations de ce skill sont issues de :
- Documentation officielle Microsoft (Power BI, Fabric, TMDL, PBIR)
- SQLBI (Marco Russo, Alberto Ferrari) — modélisation, DAX, DAX Optimizer
- Tabular Editor (Daniel Otykier) — CLI, BPA, automatisation
- Kurt Buhler — BI Engineering, bonnes pratiques
- Chris Webb — Power Query, query folding
- Communauté Power BI (PowerBI-ThemeTemplates, pbi-tools, PBI Inspector)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unnest) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
