---
name: domain-driven-design
description: Hub DDD : accès à tous les workflows et ateliers Domain-Driven Design. Utiliser pour 'DDD', 'domain driven design', 'aide DDD'. Use when this capability is needed.
metadata:
  author: mlangry
---

# Domain-Driven Design - Hub

Ce skill est le point d'entrée pour tous les outils DDD disponibles.

## Workflows disponibles

### Workflow complet

| Skill | Description | Commande |
|-------|-------------|----------|
| `ddd-full-workflow` | Processus DDD complet de bout en bout | `/ddd-full-workflow [projet]` |

### Workflows par phase

| Skill | Description | Commande |
|-------|-------------|----------|
| `ddd-discovery` | Phase Discovery : Event Storming → Domain Storytelling → Example Mapping | `/ddd-discovery [domaine]` |
| `ddd-strategic-design` | Phase Strategic : Context Mapping → Classification → Intégration | `/ddd-strategic [système]` |
| `ddd-tactical-design` | Phase Tactical : Aggregates → Events → Services | `/ddd-tactical [contexte]` |

## Ateliers individuels

| Skill | Description | Usage |
|-------|-------------|-------|
| `atelier-event-storming` | Session guidée d'Event Storming | Exploration événementielle |
| `atelier-domain-storytelling` | Session de Domain Storytelling | Capture de scénarios métier |
| `atelier-context-mapping` | Session de Context Mapping | Cartographie des contextes |
| `atelier-example-mapping` | Session d'Example Mapping | Clarification User Stories |
| `atelier-impact-mapping` | Session d'Impact Mapping | Liaison objectifs/features |
| `atelier-user-story-mapping` | Session de User Story Mapping | Visualisation parcours utilisateur |

## Mode d'utilisation

### Nouveau projet DDD

```
/ddd-full-workflow Mon Projet E-commerce

# Description du domaine métier à explorer...
```

### Phase spécifique

```
/ddd-discovery Gestion des commandes
/ddd-strategic Système de facturation
/ddd-tactical Bounded Context "Orders"
```

### Atelier ponctuel

```
/atelier-event-storming Processus de livraison
/atelier-example-mapping US-123 Validation commande
```

### Mode mémoire persistante

Ajouter `--memory` pour activer la sauvegarde/chargement des connaissances domaine entre sessions :

```
/ddd-full-workflow --memory Mon Projet E-commerce
```

La mémoire persistante sauvegarde automatiquement le glossaire, les BC, et les décisions clés après chaque phase. Au redémarrage, elle bootstrappe le contexte depuis les sessions précédentes.

> Référence : [Memory Integration](shared/memory-integration.md)

### Mode autonome (sans interaction)

Ajouter `--autonome` pour une exécution sans questions :

```
/ddd-full-workflow --autonome Description complète du domaine...
```

### Mode quick (workflow express)

Ajouter `--quick` pour un workflow raccourci (1 atelier, moins de rounds, résultat rapide) :

```
/ddd-full-workflow --quick Mon Projet E-commerce
/ddd-full-workflow --quick --autonome Description rapide du domaine...
/ddd-full-workflow --quick --swarm Mon Projet
```

Le mode quick réduit chaque phase à l'essentiel : 1 seul atelier en Discovery, Context Map directe en Strategic, une seule passe en Tactical. Idéal pour un premier cadrage ou un budget limité.

### Mode swarm (Agent Teams)

Ajouter `--swarm` pour activer le mode Agent Teams avec de vrais teammates parallèles :

```
/ddd-full-workflow --swarm Mon Projet E-commerce
/ddd-tactical --swarm Bounded Context Orders
/atelier-event-storming --swarm Processus de commande
```

Le mode swarm remplace les sous-agents simulés par de vrais teammates Agent Teams. Chaque persona (Expert Métier, Développeur, Architecte, etc.) est une instance Claude indépendante avec son propre contexte.

#### Trois modes d'exécution

| Flag | Comportement |
|------|-------------|
| *(aucun)* | **Auto-détection** : swarm si Agent Teams activé, sinon subagent |
| `--swarm` | **Force le swarm** : erreur si Agent Teams non activé |
| `--subagent` | **Force le subagent** : mode classique même si Agent Teams disponible |

Tous combinables entre eux : `--swarm --autonome --quick --memory`, `--subagent --autonome`, etc.

#### Prérequis

Agent Teams doit être activé dans `~/.claude/settings.json` :

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

#### Avantages du mode swarm

| Aspect | Subagent | Swarm |
|--------|----------|-------|
| Discussions | Simulées par un même modèle | Perspectives réellement indépendantes |
| Tactical Design | BC traités séquentiellement | BC traités en parallèle |
| Coût tokens | Modéré | Plus élevé |
| Qualité des débats | Bonne | Meilleure (indépendance des perspectives) |

## Structure de sortie

Tous les workflows génèrent une documentation dans `[projet]-ddd/` :

```
[projet]-ddd/
├── workflow-state.json      # État du workflow (reprise possible)
├── index.adoc               # Document principal
├── discovery/               # Phase Discovery
├── strategic-design/        # Phase Strategic Design
├── tactical-design/         # Phase Tactical Design
└── documentation/           # Documentation consolidée
```

## Reprise après interruption

Les workflows peuvent être repris là où ils ont été interrompus :

1. Relancer le même skill
2. Le système détecte `workflow-state.json`
3. La progression reprend automatiquement

## Ressources

Les guides de référence sont disponibles dans chaque workflow :
- `workflows/*/references/` : Guides détaillés par phase
- `shared/` : Conventions communes (Tasks, contexte, AsciiDoc)

## Intégrations avancées

### Pattern /loop (itération structurée)

Le workflow DDD intègre des patterns `/loop` pour les tâches itératives :

| Pattern | Phase | Description |
|---------|-------|-------------|
| **BC Iterator** | Tactical | Itère sur chaque BC avec vérification systématique |
| **Glossary Validator** | Post-phase | Valide la cohérence terminologique cross-documents |
| **Consistency Auditor** | Post-workflow | Audit croisé entre artefacts |

> Référence : [Loop Patterns](shared/loop-patterns.md)

### Pattern Batch + Worktree (parallélisme isolé)

Pour les phases de génération de code et de validation :

| Pattern | Phase | Description |
|---------|-------|-------------|
| **Batch Code Gen** | Implementation | Agents parallèles avec worktree par BC |
| **Batch Validation** | Validation | 3 checklists en parallèle |
| **Batch Migration** | Post-DDD | Refactoring parallèle vers patterns DDD |

> Référence : [Batch & Worktree Patterns](shared/batch-worktree-patterns.md)

## Choix du bon outil

| Besoin | Outil recommandé |
|--------|------------------|
| Nouveau projet, domaine inconnu | `/ddd-full-workflow` |
| Cadrage rapide, budget limité | `/ddd-full-workflow --quick` |
| Continuité entre sessions | `/ddd-full-workflow --memory` |
| Explorer un domaine | `/ddd-discovery` |
| Cartographier un système existant | `/ddd-strategic` ou `/atelier-context-mapping` |
| Modéliser un contexte précis | `/ddd-tactical` |
| Clarifier une User Story | `/atelier-example-mapping` |
| Planifier des releases | `/atelier-user-story-mapping` |
| Définir des objectifs | `/atelier-impact-mapping` |

## Arguments

Si des arguments sont fournis, analyser l'intention et rediriger vers le skill approprié.

$ARGUMENTS

---
> Source: [mlangry/dotclaude](https://github.com/mlangry/dotclaude) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
