---
name: gemini-review
description: Lance une review de code par Gemini AI avec auto-correction Use when this capability is needed.
metadata:
  author: adryserage
---

# Gemini Code Review

Review AI-to-AI : Gemini analyse le code, Claude corrige automatiquement.

## Workflow

```
1. Vérifier Gemini CLI installé
2. Analyser complexité (optionnel si invoqué manuellement)
3. Construire contexte (diff + fichiers + prompt original)
4. Envoyer à Gemini
5. Si issues critiques/warnings:
   - Claude applique les corrections
   - Re-soumettre à Gemini (max 3 rounds)
6. Si max rounds atteint avec issues:
   - Escalade à l'humain
   - Humain décide: continuer ou accepter
```

## Étapes d'exécution

### Étape 1: Vérification Gemini

Exécuter le script de vérification:

```bash
~/.claude/plugins/gemini-reviewer/lib/check-gemini.sh
```

Si Gemini n'est pas installé ou authentifié:
- Afficher le guide d'installation à l'utilisateur
- STOP - ne pas continuer sans Gemini

### Étape 2: Récupérer le contexte

Tu dois connaître:
1. **Le prompt original** - Quelle était la tâche demandée?
2. **Le working directory** - Où est le code?
3. **Les changements** - git diff

Si tu ne connais pas le prompt original, demande à l'utilisateur:
"Quelle était la tâche que tu voulais accomplir avec ce code?"

### Étape 3: Lancer la review

Construire le contexte et appeler Gemini:

```bash
# Construire le prompt
CONTEXT=$(~/.claude/plugins/gemini-reviewer/lib/context-builder.sh "PROMPT_ORIGINAL" "WORKING_DIR")

# Appeler Gemini
RESPONSE=$(~/.claude/plugins/gemini-reviewer/lib/gemini-call.sh review "$CONTEXT" "WORKING_DIR")
```

### Étape 4: Analyser le feedback

Parser la réponse Gemini:
- 🔴 **CRITICAL** → DOIT être corrigé
- 🟡 **WARNING** → DEVRAIT être corrigé
- 🟢 **SUGGESTION** → Optionnel
- ✅ **APPROVED** → Terminé!

### Étape 5: Auto-correction (si nécessaire)

Pour chaque issue CRITICAL ou WARNING:
1. Lire le fichier concerné
2. Appliquer la correction suggérée par Gemini
3. Vérifier que la correction est valide

Après corrections, re-lancer Étape 3 (round suivant).

### Étape 6: Escalade humain (si max rounds atteint)

Si après 3 rounds il reste des issues:

```
🚨 Après 3 rounds de correction, Gemini signale encore:

[Liste des issues restantes]

Voulez-vous:
1. Continuer les corrections (3 rounds supplémentaires)
2. Accepter le code tel quel
```

Utiliser AskUserQuestion pour demander à l'utilisateur.

## Commandes utiles

```bash
# Vérifier Gemini
~/.claude/plugins/gemini-reviewer/lib/check-gemini.sh

# Calculer score complexité
~/.claude/plugins/gemini-reviewer/lib/complexity-scorer.sh

# Construire contexte
~/.claude/plugins/gemini-reviewer/lib/context-builder.sh "prompt" "/path"

# Appeler Gemini
~/.claude/plugins/gemini-reviewer/lib/gemini-call.sh review "contexte"

# Boucle complète
~/.claude/plugins/gemini-reviewer/lib/correction-loop.sh start "prompt" "/path"
```

## Critères de succès

La review est terminée quand:
- ✅ Gemini retourne "APPROVED"
- ✅ Aucun CRITICAL ni WARNING
- ⚠️ Humain accepte malgré issues restantes

## Notes importantes

- **Lazy loading**: Ne pas envoyer le contenu complet des fichiers à Gemini. Il peut les lire lui-même si nécessaire.
- **Spécificité**: Gemini doit donner des locations précises (file:line) pour chaque issue.
- **Constructif**: Les corrections doivent être applicables directement.

---
> Source: [adryserage/gemini-reviewer](https://github.com/adryserage/gemini-reviewer) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
