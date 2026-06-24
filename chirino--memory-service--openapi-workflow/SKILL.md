---
name: openapi-workflow
description: Use when making changes to the OpenAPI contract. Workflow for updating spec and regenerating clients.
metadata:
  author: chirino
---

# OpenAPI Change Workflow

1. **Edit the spec**: `contracts/openapi/openapi.yml`

2. **Regenerate Java client**:
   ```bash
   ./java/mvnw -f java/pom.xml -pl quarkus/memory-service-rest-quarkus clean compile -am
   ```

3. **Regenerate TypeScript client**:
   ```bash
   cd frontends/chat-frontend && npm run generate
   ```

4. **Verify**:
   ```bash
   ./java/mvnw -f java/pom.xml compile
   cd frontends/chat-frontend && npm run lint && npm run build
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chirino) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
