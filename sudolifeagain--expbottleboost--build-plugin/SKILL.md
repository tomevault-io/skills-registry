---
name: build-plugin
description: Build the ExpBottleBoost Minecraft plugin JAR using Maven. Use when compiling, packaging, testing the build, or preparing for server deployment. Use when this capability is needed.
metadata:
  author: sudolifeagain
---

# Build Plugin

## Quick Build

From project root:

```bash
mvnw.cmd clean package
```

## Output

`target/ExpBottleBoost-1.0.0.jar`

## Installation

Copy JAR to server's `plugins/` folder and restart/reload.

## Troubleshooting

- **Java version error**: Ensure JAVA_HOME points to Java 17+
- **Maven not found**: Use `mvnw.cmd` wrapper included in project

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sudolifeagain) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
