---
name: devautocheck-prerequisites
description: Vérifier tous les prérequis - Mode AUTO (Phase -1) Use when this capability is needed.
metadata:
  author: atournayre
---

# Objectif

## Instructions à Exécuter

**IMPORTANT : Exécute ce workflow étape par étape :**


Vérifier que TOUS les prérequis système, PHP et Claude Code sont présents avant de lancer le workflow.

Exit code 1 et message d'erreur détaillé si quelque chose manque.

# Instructions

## 1. Vérifier les outils système

```bash
# Vérifier gh CLI
if ! command -v gh &> /dev/null; then
    echo "❌ PRÉREQUIS MANQUANT : gh CLI"
    echo "Installe : https://github.com/cli/cli"
    echo "Authentifie : gh auth login"
    exit 1
fi

# Vérifier gh authentifié
if ! gh auth status &> /dev/null; then
    echo "❌ PRÉREQUIS MANQUANT : gh CLI non authentifié"
    echo "Authentifie : gh auth login"
    exit 1
fi

# Vérifier jq
if ! command -v jq &> /dev/null; then
    echo "❌ PRÉREQUIS MANQUANT : jq"
    echo "Installe : apt-get install jq (Linux) ou brew install jq (macOS)"
    exit 1
fi

# Vérifier git
if ! command -v git &> /dev/null; then
    echo "❌ PRÉREQUIS MANQUANT : git"
    exit 1
fi
```


## 2. Vérifier les plugins Claude Code

```bash
# Vérifier plugin feature-dev (optionnel pour Explore/Design, mais requis pour automation)
# Note: La vérification exacte dépend de l'API Claude Code disponible
# Pour maintenant, on va supposer qu'il est installé
# Si absent, les phases vont échouer et afficher un message clair
```

## 3. Afficher le succès

```bash
echo "✅ Tous les prérequis sont présents"
echo ""
echo "Outils vérifiés :"
echo "  ✅ gh CLI authentifiée"
echo "  ✅ jq disponible"
echo "  ✅ git disponible"
echo ""
```

# Règles

- ✅ **Exit 1 si prérequis manquant**
- ✅ **Messages d'erreur clairs** avec instructions d'installation
- ✅ **Pas d'interaction** : fail fast
- ✅ **Rapide** : vérifications simples et directes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/atournayre) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
