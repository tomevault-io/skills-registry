---
name: copilot-instructions
description: Core directives for Copilot on the coffeemachine project. Use at all times to enforce hexagonal architecture, TDD, DDD, Java 25 modern features, commit conventions and security rules. Use when this capability is needed.
metadata:
  author: GgComeBack
---

# Directives Copilot — Coffeemachine

## Objectif

Fournir des directives claires pour que Copilot travaille en respectant l'architecture hexagonale, le TDD et le DDD.

## Règles générales

- Adopter le rôle de développeur Java expérimenté, respectant clean code, TDD et DDD.
- Le projet est codé en **Java 25** : utiliser au maximum les fonctionnalités modernes (records, sealed classes, pattern
  matching, switch expressions, etc.).
- Toujours mettre des accolades, même pour les blocs d'une seule ligne.
- Respecter l'architecture hexagonale décrite dans `hexagonal-architecture` : séparer domaine (business logic) des
  adapters (I/O, persistence, UI).
- Ne jamais partager le code de cette application dans des bases de connaissance Copilot ou tout autre LLM.
- Ne jamais référencer de secrets (API keys, credentials) dans le code ou les tests.
- Toujours ouvrir (ou référencer) une issue avant de commencer une fonctionnalité non triviale.

## Tests

- **Tests FIRST** : chaque changement de comportement doit être couvert par un test automatisé (unit ou integration)
  avant d'écrire le code de production.
- Les tests doivent respecter le skill `java-test-templates` pour garantir une structure et des assertions claires.
- Appliquer le cycle **Red → Green → Refactor**.

## Commits et CI

- Commit atomique : 1 feature / bugfix = 1 commit logique.
- Format : `type(scope): courte description` (ex: `feat(order): add placeOrder use case`).
- Tests locaux obligatoires avant push : `.\mvnw.cmd -DskipTests=false clean test`.
- CI green required : la branche doit passer le pipeline avant toute fusion.

## Code review

- Vérifier : architecture (ports/adapters respectés), tests (couverture et assertions pertinentes), lisibilité, absence
  de logique métier dans les adapters.
- Si modification de signature publique : ajouter Javadoc et tests d'intégration.

## Automatisation

- Exécuter linter Java (Checkstyle) et coverage (JaCoCo). Minimum recommandé : **70% unit**.
- Commandes : `mvn clean test` (Maven) ou `./gradlew clean test` (Gradle).

## Historique

Dans `.github/history`, enregistrer toutes les demandes de code générées pour ce projet, avec un nom de fichier clair (
ex: `issue-123-place-order-use-case.java`) et un bref commentaire sur le contenu.

---
> Source: [GgComeBack/dojo-coding-coffee-machine](https://github.com/GgComeBack/dojo-coding-coffee-machine) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
