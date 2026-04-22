---
name: add-traefik-labels-coolify
description: Agrega automáticamente labels de Traefik y configura networking para Coolify a un docker-compose existente, convirtiendo servicios locales en production-ready sin reescribir el archivo. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Agregar Labels de Traefik a Docker-Compose Existente

## Propósito

Tomar un `docker-compose.yml` existente (local o sin optimizar para Coolify) y **agregar automáticamente**:
1. ✅ Labels de Traefik completos (HTTPS, redirect HTTP→HTTPS, etc.)
2. ✅ Networking Coolify (`network: coolify + default`)
3. ✅ Validaciones de puertos y healthchecks
4. ✅ Comentarios explicativos

**Sin reescribir el archivo de cero. Solo agregar lo necesario.**

---

## Proceso (4 Pasos)

### PASO 1: Análisis del Compose Actual

Primero, examinar qué existe:

```bash
# Preguntas a hacer:
1. ¿Cuál es el nombre del stack? (ej: "portal", "api", "blog")
2. ¿Cuál es el dominio público? (ej: "portal.da-tica.com")
3. ¿Qué servicio es público (expuesto a internet)? (ej: "web", "api", "nginx")
4. ¿En qué puerto escucha ese servicio internamente? (ej: 8080, 3000, 80)
5. ¿Necesita comunicarse con servicios internos (db, redis)? (s/n)
```

**Si el usuario no proporciona info:**
```
⚠️ Necesito información del stack:

📋 Analiza el docker-compose actual y dime:
  - Nombre del stack (para prefijo de labels)
  - Dominio público (ej: api.da-tica.com)
  - Cuál servicio es público (web, api, etc.)
  - Puerto interno del servicio público
  - Si necesita conectarse con otros servicios

O proporciona el archivo docker-compose.yml para analizarlo.
```

---

### PASO 2: Generación de Labels

Basado en la info, generar labels Traefik completos:

```yaml
# Plantilla de labels para servicio público
labels:
  - "traefik.enable=true"
  # Router HTTPS (principal)
  - "traefik.http.routers.{stack}-{servicio}.rule=Host(`{dominio}`)"
  - "traefik.http.routers.{stack}-{servicio}.entrypoints=websecure"
  - "traefik.http.routers.{stack}-{servicio}.tls.certresolver=letsencrypt"
  - "traefik.http.routers.{stack}-{servicio}.middlewares={stack}-redirect"
  # Router HTTP (solo redirect)
  - "traefik.http.routers.{stack}-{servicio}-http.rule=Host(`{dominio}`)"
  - "traefik.http.routers.{stack}-{servicio}-http.entrypoints=web"
  - "traefik.http.routers.{stack}-{servicio}-http.middlewares={stack}-redirect"
  # Middleware redirect HTTP → HTTPS
  - "traefik.http.middlewares.{stack}-redirect.redirectscheme.scheme=https"
  - "traefik.http.middlewares.{stack}-redirect.redirectscheme.permanent=true"
  # Puerto interno
  - "traefik.http.services.{stack}-{servicio}.loadbalancer.server.port={puerto}"
```

**Ejemplo real:**
```yaml
# Stack: portal, Dominio: portal.da-tica.com, Servicio: web, Puerto: 8080
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.portal-web.rule=Host(`portal.da-tica.com`)"
  - "traefik.http.routers.portal-web.entrypoints=websecure"
  - "traefik.http.routers.portal-web.tls.certresolver=letsencrypt"
  - "traefik.http.routers.portal-web.middlewares=portal-redirect"
  - "traefik.http.routers.portal-web-http.rule=Host(`portal.da-tica.com`)"
  - "traefik.http.routers.portal-web-http.entrypoints=web"
  - "traefik.http.routers.portal-web-http.middlewares=portal-redirect"
  - "traefik.http.middlewares.portal-redirect.redirectscheme.scheme=https"
  - "traefik.http.middlewares.portal-redirect.redirectscheme.permanent=true"
  - "traefik.http.services.portal-web.loadbalancer.server.port=8080"
```

---

### PASO 3: Configuración de Networking

Modificar la sección `services` y agregar `networks`:

#### Para el servicio público:
```yaml
services:
  web:
    image: node:18-alpine
    # ... resto de config ...
    networks:
      - coolify      # AGREGAR - Red de Traefik
      - default      # AGREGAR - Red interna para hablar con DB/Redis
    expose:
      - "8080"
    labels:
      # ... labels generados en PASO 2 ...
```

#### Para servicios internos (DB, Redis, etc.):
```yaml
  db:
    image: postgres:16-alpine
    # ... resto de config ...
    # NO agregar network: coolify
    # NO agregar labels
    # Comunicación solo vía nombre de servicio (db:5432)
```

#### Agregar sección `networks` al final:
```yaml
networks:
  coolify:
    external: true  # ← Red gestionada por Coolify, no crear nueva
```

---

### PASO 4: Validaciones y Ajustes

Verificar que todo sea correcto:

```
✅ CHECKLIST DE LABELS:

1. ¿Servicio público tiene labels? ✅
2. ¿Puerto en expose coincide con label loadbalancer.server.port? ✅
3. ¿Servicio público está en ambas redes (coolify + default)? ✅
4. ¿Servicios internos NO tienen labels? ✅
5. ¿Red coolify está marcada como external: true? ✅
6. ¿Dominio está hardcodeado en labels? (debe ser `{dominio}` para variable después) ✅
7. ¿Prefijo {stack}-{servicio} es consistente en todos los labels? ✅
8. ¿Hay healthchecks en todos los servicios? ✅
```

Si algo falla, mostrar error específico:
```
❌ PROBLEMA DETECTADO:

Label loadbalancer.server.port=3000 pero expose=["8080"]
→ Deben coincidir. ¿Cuál es el puerto correcto? (3000 o 8080)
```

---

## Cómo Usar Este Skill

### Escenario 1: Tienes docker-compose sin labels

**Tú:**
> "Tengo un docker-compose con web (puerto 8080) y postgres. Stack: 'portal', dominio: portal.da-tica.com. Agrega labels de Traefik."

**Yo:**
1. Analizan compose ✅
2. Genero labels para web ✅
3. Configuro networking (coolify + default para web, nada para postgres) ✅
4. Valido todo ✅
5. Te muestro el compose modificado con secciones nuevas claramente marcadas

---

### Escenario 2: Tienes compose pero no sé estructura

**Tú:**
> "Agrega labels de Traefik. Aquí está el compose."
> [adjunta archivo docker-compose.yml]

**Yo:**
1. Analizo el archivo ✅
2. Pregunto info faltante (dominio, stack name) ✅
3. Genero y aplico labels ✅
4. Te muestro resultado

---

### Escenario 3: Múltiples servicios públicos

**Tú:**
> "Tengo web (8080) y api (3000), ambos públicos. web.da-tica.com y api.da-tica.com"

**Yo:**
1. Genero labels para AMBOS servicios ✅
2. Ambos en redes coolify + default ✅
3. Prefijos diferentes: `stack-web` y `stack-api` ✅
4. Resultado completo ✅

---

## Estructura del Output

Cuando generes el resultado, mostrar así:

```yaml
# ============================================================
# MODIFICACIONES APLICADAS A docker-compose.yml
# Stack: portal
# Dominio: portal.da-tica.com
# Servicio público: web (puerto 8080)
# ============================================================

version: "3.8"

services:
  web:
    image: node:18-alpine
    restart: unless-stopped
    expose:
      - "8080"
    # ============ LABELS AGREGADOS ============
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.portal-web.rule=Host(`portal.da-tica.com`)"
      # ... (resto de labels) ...
    # ==========================================
    # ============ NETWORKING AGREGADO ============
    networks:
      - coolify
      - default
    # ============================================
    # ... resto de config original ...

  postgres:
    image: postgres:16-alpine
    restart: unless-stopped
    # ... sin cambios (no es público) ...

# ============ SECCIÓN NETWORKS AGREGADA ============
networks:
  coolify:
    external: true
# ===================================================

volumes:
  postgres_data:
```

---

## Instrucciones Post-Aplicación

Una vez agregados los labels, proporcionar checklist:

```
📋 PRÓXIMOS PASOS EN COOLIFY:

1. ✅ Desactivar auto-generación de labels
   - En Coolify UI → Proyecto → Settings
   - Buscar "Auto-generate labels"
   - Desactivar ✓

2. ✅ Verificar variables de entorno
   - Estas variables deben estar en Coolify:
     • DB_PASSWORD=...
     • APP_PORT=8080
     • etc.

3. ✅ Dominio en Cloudflare
   - portal.da-tica.com → apunta a 217.216.81.73
   - Preferiblemente "proxied" en Cloudflare

4. ✅ Deploy
   - Push a GitHub
   - Coolify detecta cambios
   - Traefik aplicará labels automáticamente

5. ✅ Verificar
   - curl -I https://portal.da-tica.com
   - Debe devolver 200 OK
   - TLS debe estar activo
```

---

## Casos Especiales

### Caso 1: Servicio con subdominio dinámico

Si quieres cambiar fácilmente el dominio:

```yaml
# ❌ HARDCODEADO (malo)
labels:
  - "traefik.http.routers.portal-web.rule=Host(`portal.da-tica.com`)"

# ✅ VARIABLE (mejor, pero no es YAML válido por sí solo)
# Luego en Coolify, puedes usar find/replace o variable global
labels:
  - "traefik.http.routers.portal-web.rule=Host(`${DOMAIN}`)"
```

Avisar al usuario:
```
⚠️ NOTA: Si quieres dominio variable:
Cambiar:
  Host(`portal.da-tica.com`)
Por:
  Host(`${DOMAIN}`)

Luego en Coolify, definir: DOMAIN=portal.da-tica.com
```

### Caso 2: WWW + apex domain

Si necesitas soportar `portal.da-tica.com` y `www.portal.da-tica.com`:

```yaml
labels:
  - "traefik.http.routers.portal-web.rule=Host(`portal.da-tica.com`) || Host(`www.portal.da-tica.com`)"
```

Avisar:
```
⚠️ Detecté múltiples dominios solicitados.
Labels configurados para: portal.da-tica.com + www.portal.da-tica.com
```

### Caso 3: Servicio interno con puerto alto

```yaml
# Algunos servicios internos podrían necesitar ser accesibles
# (ej: admin panel en puerto 9000 pero solo internamente)

services:
  admin:
    image: admin-panel:1.0.0
    expose:
      - "9000"
    # SIN labels de Traefik
    # SIN red coolify
    # Acceso solo vía docker exec o desde otro contenedor
```

---

## Validaciones Automáticas

El skill SIEMPRE valida:

1. **Puertos:**
   - `expose:` coincide con label `server.port` ✅
   - El puerto es único en el stack ✅
   - No hay `ports:` en servicios públicos ✅

2. **Labels:**
   - Router HTTPS presente ✅
   - Router HTTP presente ✅
   - Middleware redirect presente ✅
   - Service port presente ✅
   - Prefijos consistentes ✅

3. **Networking:**
   - Servicio público en `coolify` ✅
   - Servicio público en `default` (si hay deps internas) ✅
   - Servicios internos SIN `coolify` ✅
   - `networks.coolify.external: true` ✅

4. **Dominio:**
   - Formato válido ✅
   - Consistente en todos los labels ✅

5. **Healthcheck:**
   - Presente en todos los servicios ✅
   - Comando válido (binario existe en imagen) ✅

---

## Integración con Otros Skills

Después de usar este skill:

1. **validate-traefik-labels** → verifica que labels son correctos ✅
2. **validate-coolify-compose** → valida resto del compose ✅
3. **coolify-github-workflow** → commit y push ✅
4. **git-commit-and-push-coolify** → automático ✅

Antes de usar este skill:

1. **coolify-architect-guidance** → entiende estructura ✅
2. **validate-common-coolify-errors** → evita errores conocidos ✅

---

## Ejemplo Completo: De Local a Production

### Compose Local Original:
```yaml
version: "3.8"

services:
  web:
    image: node:18-alpine
    ports:
      - "8080:8080"
    environment:
      - PORT=8080
      - DATABASE_URL=postgres://user:pass@db:5432/mydb
    depends_on:
      - db

  db:
    image: postgres:16-alpine
    environment:
      - POSTGRES_PASSWORD=mypass
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### Después de agregar labels (usando este skill):

```yaml
version: "3.8"

services:
  web:
    image: node:18-alpine
    restart: unless-stopped
    expose:  # ← Cambió de ports: a expose:
      - "8080"
    environment:
      - PORT=8080
      - DATABASE_URL=postgres://user:${DB_PASSWORD}@db:5432/mydb
    depends_on:
      db:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://127.0.0.1:8080/health"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s
    networks:  # ← AGREGADO
      - coolify
      - default
    labels:  # ← AGREGADO COMPLETO
      - "traefik.enable=true"
      - "traefik.http.routers.myapp-web.rule=Host(`myapp.da-tica.com`)"
      - "traefik.http.routers.myapp-web.entrypoints=websecure"
      - "traefik.http.routers.myapp-web.tls.certresolver=letsencrypt"
      - "traefik.http.routers.myapp-web.middlewares=myapp-redirect"
      - "traefik.http.routers.myapp-web-http.rule=Host(`myapp.da-tica.com`)"
      - "traefik.http.routers.myapp-web-http.entrypoints=web"
      - "traefik.http.routers.myapp-web-http.middlewares=myapp-redirect"
      - "traefik.http.middlewares.myapp-redirect.redirectscheme.scheme=https"
      - "traefik.http.middlewares.myapp-redirect.redirectscheme.permanent=true"
      - "traefik.http.services.myapp-web.loadbalancer.server.port=8080"

  db:
    image: postgres:16-alpine
    restart: unless-stopped
    environment:
      - POSTGRES_PASSWORD=${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    # ← Sin red coolify, sin labels

networks:  # ← AGREGADO
  coolify:
    external: true

volumes:
  postgres_data:
```

---

## Checklist Final

Antes de dar como "completado":

- [ ] ¿Labels agregados al servicio público?
- [ ] ¿Networking configurado (coolify + default)?
- [ ] ¿Servicios internos sin labels?
- [ ] ¿Puertos coinciden (expose + label)?
- [ ] ¿Red coolify es external: true?
- [ ] ¿Prefijos {stack}-{servicio} son consistentes?
- [ ] ¿Healthchecks presentes en todos?
- [ ] ¿Variables sensibles usan ${VAR}?
- [ ] ¿Dominio es válido y único?
- [ ] ¿Archivo YAML tiene sintaxis correcta?

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
