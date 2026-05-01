---
name: espeak-ng
description: TTS with espeak-ng Use when this capability is needed.
metadata:
  author: openclaw
---

# Espeak-ng

This skill allows you to use espeak-ng to generate speech.

## For Smart Agent
**Command:**   python espeak_skill.py <text>
**Example:**   python espeak_skill.py "Hello world"

## For Not Smart Agent
**Command:**
python ./skills/espeak-ng/espeak_skill.py <text>
**Example:**
python ./skills/espeak-ng/espeak_skill.py "Hello world"

## Tools exec
**Command:** espeak-ng <text>
**Example:** espeak-ng "Hello world"


## Requirements
- espeak-ng must be installed on the system
- On Linux/macOS, ensure you have `espeak-ng` installed:
- Ubuntu/Debian: `sudo apt-get install espeak-ng`
- CentOS/RHEL: `sudo yum install espeak-ng`
- macOS: `brew install espeak-ng`
- windows 11 : espeak-ng.msi from https://github.com/espeak-ng/espeak-ng/releases

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
