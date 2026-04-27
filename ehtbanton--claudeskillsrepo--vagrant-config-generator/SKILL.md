---
name: vagrant-config-generator
description: Generate Vagrant configuration files for local development environments. Triggers on "create vagrantfile", "generate vagrant config", "vagrant setup", "local vm config". Use when this capability is needed.
metadata:
  author: ehtbanton
---

# Vagrant Config Generator

Generate Vagrant configuration files for reproducible development environments.

## Output Requirements

**File Output:** `Vagrantfile`
**Format:** Valid Ruby Vagrantfile
**Standards:** Vagrant 2.x

## When Invoked

Immediately generate a complete Vagrantfile for the development environment.

## Example Invocations

**Prompt:** "Create Vagrantfile for Node.js development"
**Output:** Complete `Vagrantfile` with Ubuntu, Node.js, and port forwarding.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ehtbanton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
