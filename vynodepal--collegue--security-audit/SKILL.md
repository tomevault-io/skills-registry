---
name: security-audit
description: Réalise un audit de sécurité complet d'un projet en orchestrant secret_scan, dependency_guard et iac_guardrails_scan. Utilise cette skill quand l'utilisateur demande un audit sécurité, une revue de sécurité, ou veut vérifier la posture de sécurité d'un codebase. Use when this capability is needed.
metadata:
  author: vynodepal
---

# Audit de sécurité — Workflow Collègue

Tu réalises un audit de sécurité complet en 4 phases. Pour chaque phase, utilise l'outil MCP Collègue correspondant.

## Phase 1 : Scan des secrets exposés

Appelle `secret_scan` avec tous les fichiers du projet.

**Paramètres recommandés :**
- `files` : tous les fichiers source (exclure node_modules, .git, dist)
- `severity_threshold` : `"low"` (capturer tout)
- `check_vulnerabilities` : `true`

**Ce qu'on cherche :**
- Clés API hardcodées (AWS, GCP, Azure, OpenAI, Stripe, etc.)
- Tokens d'authentification (GitHub, GitLab, Slack, JWT)
- Mots de passe dans le code ou les URLs
- Clés privées (RSA, SSH, PGP)
- Secrets dans les variables d'environnement non protégées
- Strings haute entropie suspectes (hex/base64)

**Actions si trouvé :**
1. Révoquer immédiatement le secret compromis
2. Générer un nouveau secret
3. Stocker dans un vault (AWS Secrets Manager, HashiCorp Vault, .env non commité)
4. Vérifier l'historique Git : `git log --all -p -S 'SECRET_VALUE'`
5. Ajouter le fichier au `.gitignore` si nécessaire

## Phase 2 : Validation des dépendances

Appelle `dependency_guard` avec le fichier de dépendances.

**Paramètres recommandés :**
- `content` : contenu de `package-lock.json` (JS) ou `requirements.txt` / `pyproject.toml` (Python)
- `language` : `"python"` ou `"javascript"`
- `check_vulnerabilities` : `true`
- `check_existence` : `true`

**Ce qu'on cherche :**
- Vulnérabilités connues (CVE) via la base OSV de Google
- Packages inexistants (hallucinations IA)
- Typosquatting (ex: `requets` au lieu de `requests`)
- Packages dépréciés avec alternatives connues
- Packages dans la blocklist (malware connu)

**Scoring de risque :**
- 🔴 **Critique** : CVE avec exploit connu, package malveillant
- 🟠 **Élevé** : CVE sans patch disponible, package déprécié avec vulnérabilité
- 🟡 **Moyen** : CVE avec patch disponible, package déprécié
- 🟢 **Faible** : version non optimale, package ancien

## Phase 3 : Scan de l'infrastructure as code

Appelle `iac_guardrails_scan` avec les fichiers d'infrastructure.

**Fichiers à scanner :**
- `*.tf`, `*.tfvars` — Terraform
- `*.yaml`, `*.yml` dans `k8s/`, `kubernetes/`, `manifests/` — Kubernetes
- `Dockerfile`, `docker-compose.yml` — Docker

**Paramètres recommandés :**
- `files` : liste des fichiers IaC trouvés
- `policy_profile` : `"strict"` (pour un audit complet)
- `analysis_depth` : `"deep"` (scoring IA)

**Ce qu'on cherche :**
- Containers privilégiés ou root
- Ports ouverts au monde (0.0.0.0/0)
- Secrets hardcodés dans Terraform/Docker
- Images sans tag de version
- Absence de limites de ressources
- IAM avec wildcards

## Phase 4 : Rapport consolidé

Après les 3 phases, produis un rapport structuré :

```markdown
# Rapport d'audit de sécurité

**Projet** : [nom]
**Date** : [date]
**Score global** : [X/100]

## Résumé exécutif
- Secrets trouvés : X (Y critiques)
- Vulnérabilités dépendances : X (Y critiques)
- Problèmes IaC : X (Y critiques)

## Findings critiques (action immédiate)
1. [finding + action recommandée]

## Findings élevés (corriger rapidement)
1. [finding + action recommandée]

## Findings moyens (planifier)
1. [finding + action recommandée]

## Recommandations générales
- [recommandation]
```

**Scoring :**
- 90-100 : Excellent — Posture de sécurité solide
- 70-89 : Bon — Quelques améliorations à planifier
- 50-69 : Moyen — Actions correctives nécessaires
- 0-49 : Critique — Risques majeurs à traiter immédiatement

## Checklist de référence

Pour les détails OWASP et les patterns de sécurité, consulte [reference.md](reference.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vynodepal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
