---
name: banco-fx-workflow
description: Trabajar dentro del proyecto BancoFx basado en Maven y JavaFX modular. Usar cuando Codex necesite analizar, modificar o revisar este repo, respetando AGENTS.md, ejecutando comandos desde bancoproyect/, revisando pom.xml, module-info.java, App.java, controladores y FXML en src/main/resources/society/. Use when this capability is needed.
metadata:
  author: jhateru
---

# BancoFx Workflow

Lee `AGENTS.md` antes de proponer cambios o ejecutar comandos.

Ejecuta todos los comandos Maven desde `bancoproyect/`.

Revisa primero estos archivos:

1. `bancoproyect/pom.xml`
2. `bancoproyect/src/main/java/module-info.java`
3. `bancoproyect/src/main/java/society/App.java`
4. `bancoproyect/src/main/java/society/*.java`
5. `bancoproyect/src/main/resources/society/*.fxml`

Usa `references/project-structure.md` cuando necesites contexto del repo, comandos y convenciones locales.

Usa `references/review-checklist.md` cuando el pedido sea una revision, diagnostico o validacion tecnica.

Si agregas nuevos paquetes que interactuen con JavaFX o FXML, verifica si `module-info.java` necesita `opens` o `exports` adicionales.

---
> Source: [jhateru/BancoFx](https://github.com/jhateru/BancoFx) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
