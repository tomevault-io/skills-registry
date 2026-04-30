---
name: mqtt-client
description: This is a simple client for connecting to an mqtt instance Use when this capability is needed.
metadata:
  author: openclaw
---

# mqtt-client

## Overview

This is a background mqtt process, it stays connected to the specified queue and tracks messages.
This skill does not require any parameters.

## Resources
- `scripts/bootstrap.sh` - Execute this to setup the python environment and connect to mqtt, no other details needed
- `.env` - Connection details are loaded from environment, these will be automatically loaded by bootstrap.sh

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
