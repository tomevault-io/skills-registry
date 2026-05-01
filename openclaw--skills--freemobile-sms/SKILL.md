---
name: freemobile-sms
description: Envoyer des SMS à ton humain via Free Mobile Use when this capability is needed.
metadata:
  author: openclaw
---

# Envoyer des SMS à ton humain

## Quand utiliser cette skill

Utilise cette skill quand tu veux envoyer un SMS à ton humain.

## Exemple d’utilisation

- `scripts/FreeMobile_sms.py --message "Ton rendez-vous chez le dentiste est dans 1 heure" --timeout 15`

## Configuration

Le script d’envoi de SMS utilises ces variables d’environnement. Tu n’as rien à faire en plus.

- `FREEMOBILE_SMS_USER` : identifiant Free Mobile
- `FREEMOBILE_SMS_API_KEY` : clé API

## Limitations

- Maximum 200-250 SMS/jour (limite Free Mobile)
- 160 caractères par SMS
- Délai minimal 10 secondes entre envois
- Envoi uniquement vers le numéro de l’abonné Free Mobile


## Documentation

Consulte la [documentation](references/REFERENCE.md) pour les détails.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
