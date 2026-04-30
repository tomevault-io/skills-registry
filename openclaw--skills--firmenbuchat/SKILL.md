---
name: firmenbuchat
description: CLI für den Zugriff auf das österreichische Firmenbuch (HVD WebServices). Use when this capability is needed.
metadata:
  author: openclaw
---

# firmenbuchat

Setup (API-Key)
- `firmenbuchat config set-key`
- `export FIRMENBUCH_API_KEY="dein-key"`
- `.env`: `cp .env.example .env` und dann `firmenbuchat --env-file /pfad/zu/.env <command>`

Hilfe (alle Commands)
- `firmenbuchat help`

Common Commands
- Version: `firmenbuchat version`
- Info: `firmenbuchat info`
- Konfig anzeigen: `firmenbuchat config show`
- Konfig löschen: `firmenbuchat config delete --force`

Firmenbuchauszug
- `firmenbuchat auszug <FNR> [--stichtag YYYY-MM-DD] [--umfang "Kurzinformation"|"aktueller Auszug"|"historischer Auszug"]`

Firmensuche
- `firmenbuchat suche firma <SUCHBEGRIFF> [--bereich 1-6] [--exakt] [--gericht 007] [--rechtsform GES]`

Urkundensuche
- `firmenbuchat suche urkunde <FNR> [--output table|json|raw] [--limit 50] [--offset 0]`

Urkunden
- Info: `firmenbuchat urkunde info <URKUNDEN_KEY>`
- Download: `firmenbuchat urkunde download <URKUNDEN_KEY> [--output PATH]`

Veränderungen
- Firmen: `firmenbuchat veraenderungen firmen [--von YYYY-MM-DD] [--bis YYYY-MM-DD] [--gericht 007] [--rechtsform GES]`
- Urkunden: `firmenbuchat veraenderungen urkunden [--von YYYY-MM-DD] [--bis YYYY-MM-DD]`

Diagnose
- `firmenbuchat doctor [--env-file PATH]`

Globale Optionen
- `-o, --output`: `table` (default), `json`, `raw`
- `-k, --api-key`: API-Key direkt übergeben
- `-e, --env-file`: Pfad zu `.env` Datei
- `--limit`: Anzahl Ergebnisse (Tabellen)
- `--offset`: Start-Offset

Notes
- `veraenderungen urkunden` kann bei großen Zeiträumen 5xx liefern; kleinere Zeitfenster nutzen.
- Downloads brauchen einen `URKUNDEN_KEY` aus `suche urkunde`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
