---
name: generate-compose-with-labels
description: Genera docker-compose.yml completo con labels de Traefik/Coolify integrados automáticamente, incluyendo redes, healthchecks, variables y documentación post-deploy. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Generar Docker Compose con Labels de Traefik

## Propósito

Generar un **docker-compose.yml de producción** completo y listo para Coolify que incluya automáticamente:
- ✅ Labels de Traefik para enrutamiento HTTP/HTTPS
- ✅ Redirects HTTP → HTTPS
- ✅ Configuración de redes (coolify + default)
- ✅ Healthchecks validados
- ✅ Variables de entorno externalizadas
- ✅ Volúmenes persistentes
- ✅ Documentación post-deploy

Sin necesidad de configurar labels manualmente en la UI de Coolify.

---

## Flujo de Trabajo (5 Pasos)

### PASO 1: Recopilar Información del Stack

Cuando pidas generar un compose, necesito conocer:

```
1. NOMBRE DEL STACK
   Ej: "portal", "api-server", "cms-website"
   → Usado como prefijo en labels de Traefik

2. DOMINIO PÚBLICO
   Ej: "portal.da-tica.com"
   → Usado en rule de Traefik

3. SERVICIOS A INCLUIR
   Ej:
   - Node.js API (puerto 3000)
   - PostgreSQL (puerto 5432, solo interno)
   - Redis (puerto 6379, solo interno)
   - React frontend (puerto 8080)

4. PUERTO INTERNO DEL SERVICIO PÚBLICO
   Ej: 8080 (si es frontend), 3000 (si es API)
   → Usado en loadbalancer.server.port

5. IMÁGENES Y VERSIONES
   Ej: "node:18.20.1-alpine3.18", "postgres:16-alpine"
   → Nunca usar :latest

6. VARIABLES SENSIBLES
   Ej: POSTGRES_PASSWORD, API_KEY, JWT_SECRET
   → Se externalizan en Coolify
```

**Si no proporcionas algo, pregunto.**

---

### PASO 2: Estructura Base del Compose

Genero estructura con 3 secciones clave:

```yaml
version: "3.8"

# ═══════════════════════════════════════
# SERVICIOS
# ═══════════════════════════════════════
services:
  # Servicio público (con labels)
  api:
    # config
    networks:
      - coolify
      - default
    labels:
      # labels de Traefik

  # Servicios internos (sin labels, sin red coolify)
  postgres:
    # config
    networks:
      - default  # SOLO default, no coolify

# ═══════════════════════════════════════
# REDES
# ═══════════════════════════════════════
networks:
  coolify:
    external: true

# ═══════════════════════════════════════
# VOLÚMENES
# ═══════════════════════════════════════
volumes:
  postgres_data:
```

---

### PASO 3: Generar Labels de Traefik

Para cada servicio público, genero labels con estructura uniforme:

**Patrón:**
```
{stack}-{servicio}  ← Identificador único
{stack}-redirect    ← Middleware para HTTP→HTTPS
```

**Ejemplo: Stack "portal", servicio "api":**

```yaml
labels:
  # Habilitar Traefik
  - "traefik.enable=true"
  
  # Router HTTPS (principal)
  - "traefik.http.routers.portal-api.rule=Host(`portal.da-tica.com`)"
  - "traefik.http.routers.portal-api.entrypoints=websecure"
  - "traefik.http.routers.portal-api.tls.certresolver=letsencrypt"
  - "traefik.http.routers.portal-api.middlewares=portal-redirect"
  
  # Router HTTP (solo redirect)
  - "traefik.http.routers.portal-api-http.rule=Host(`portal.da-tica.com`)"
  - "traefik.http.routers.portal-api-http.entrypoints=web"
  - "traefik.http.routers.portal-api-http.middlewares=portal-redirect"
  
  # Middleware redirect HTTP → HTTPS
  - "traefik.http.middlewares.portal-redirect.redirectscheme.scheme=https"
  - "traefik.http.middlewares.portal-redirect.redirectscheme.permanent=true"
  
  # Puerto interno del contenedor
  - "traefik.http.services.portal-api.loadbalancer.server.port=3000"
```

**Automáticamente ajusto:**
- ✅ `{stack}` → nombre del stack
- ✅ `{servicio}` → nombre del servicio
- ✅ `Host()` → dominio que proporcionaste
- ✅ `server.port` → puerto interno del servicio

---

### PASO 4: Configuración Completa del Servicio Público

Genero configuración con todas las reglas obligatorias:

```yaml
api:
  image: node:18.20.1-alpine3.18          # ✅ Versión fija
  restart: unless-stopped                 # ✅ Restart policy
  expose:
    - "3000"                              # ✅ Expose en lugar de ports
  environment:
    - NODE_ENV=production
    - PORT=3000                           # ✅ App debe escuchar en 0.0.0.0
    - DATABASE_URL=${DATABASE_URL}        # ✅ Variables sensibles
    - API_KEY=${API_KEY}
  volumes:
    - app_logs:/app/logs                  # ✅ Volúmenes persistentes si aplica
  depends_on:
    postgres:
      condition: service_healthy          # ✅ Espera a DB
  healthcheck:                            # ✅ Obligatorio
    test: ["CMD", "curl", "-f", "http://127.0.0.1:3000/health"]
    interval: 30s
    timeout: 5s
    retries: 3
    start_period: 10s
  networks:
    - coolify                             # ✅ Red Traefik
    - default                             # ✅ Red interna
  labels:                                 # ✅ Labels de Traefik
    - "traefik.enable=true"
    - "traefik.http.routers.portal-api.rule=Host(`portal.da-tica.com`)"
    # ... más labels ...
```

---

### PASO 5: Documentación Post-Deploy

Al final del archivo, genero bloque de comentarios con instrucciones:

```yaml
#
# ═══════════════════════════════════════════════════════════
# POST-DEPLOY: PASOS OBLIGATORIOS EN COOLIFY
# ═══════════════════════════════════════════════════════════
#
# 1. DESACTIVAR AUTO-GENERACIÓN DE LABELS
#    En Coolify UI: Proyecto → Configuración → "Auto-generate Traefik labels" → OFF
#
# 2. VARIABLES DE ENTORNO A CONFIGURAR EN COOLIFY
#    - DATABASE_URL=postgres://user:password@postgres:5432/mydb
#    - API_KEY=tu_clave_api
#    - JWT_SECRET=tu_jwt_secret
#
# 3. PUERTO INTERNO DETECTADO POR TRAEFIK
#    Puerto: 3000 (debe coincidir con expose y server.port)
#
# 4. VERIFICACIONES PRE-DEPLOY
#    a) Node.js API escucha en 0.0.0.0:3000 (no solo localhost)
#    b) Healthcheck endpoint /health responde con 200 OK
#    c) PostgreSQL accesible vía "postgres" en red Docker
#
# 5. DNS EN CLOUDFLARE
#    portal.da-tica.com → A record → 217.216.81.73 (VPS IP)
#    Proxy: Activado (naranja) en Cloudflare
#
# 6. VERIFICACIÓN POST-DEPLOY
#    curl -I https://portal.da-tica.com
#    → Debe responder 200/301 (no 502/503)
#
# 7. CERTIFICADO LETS ENCRYPT
#    Traefik automáticamente obtiene certificado
#    Revisar en navegador: 🔒 HTTPS válido
#
```

---

## Ejemplo Completo Generado

**Tú pides:**
> "Genera docker-compose para un stack llamado 'myapp' con Node.js API en puerto 3000, PostgreSQL, Redis y frontend React en puerto 8080. Dominio: app.da-tica.com"

**Yo genero:**

```yaml
---
# Stack: myapp
# Dominio: app.da-tica.com
# Servicios: api (Node.js 3000), web (React 8080), postgres, redis
# Descripción: Stack completo con API, DB, cache y frontend
#
# Requisitos Coolify:
# - Desactivar auto-generación de labels de Traefik en UI
# - Variables a configurar: DATABASE_URL, API_KEY, JWT_SECRET, REDIS_URL
# - Puerto expuesto a internet: 3000 (API), 8080 (frontend)

version: "3.8"

services:
  # ════════════════════════════════════════════════════════
  # SERVICIO PÚBLICO: API (con labels de Traefik)
  # ════════════════════════════════════════════════════════
  api:
    image: node:18.20.1-alpine3.18
    container_name: myapp_api
    restart: unless-stopped
    expose:
      - "3000"
    environment:
      - NODE_ENV=production
      - PORT=3000
      - DATABASE_URL=${DATABASE_URL}
      - REDIS_URL=${REDIS_URL}
      - API_KEY=${API_KEY}
      - JWT_SECRET=${JWT_SECRET}
    volumes:
      - app_logs:/app/logs
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://127.0.0.1:3000/health"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s
    networks:
      - coolify
      - default
    labels:
      - "traefik.enable=true"
      # Router HTTPS (principal)
      - "traefik.http.routers.myapp-api.rule=Host(`app.da-tica.com`) && PathPrefix(`/api`)"
      - "traefik.http.routers.myapp-api.entrypoints=websecure"
      - "traefik.http.routers.myapp-api.tls.certresolver=letsencrypt"
      - "traefik.http.routers.myapp-api.middlewares=myapp-redirect"
      # Router HTTP (solo redirect)
      - "traefik.http.routers.myapp-api-http.rule=Host(`app.da-tica.com`) && PathPrefix(`/api`)"
      - "traefik.http.routers.myapp-api-http.entrypoints=web"
      - "traefik.http.routers.myapp-api-http.middlewares=myapp-redirect"
      # Middleware redirect HTTP → HTTPS
      - "traefik.http.middlewares.myapp-redirect.redirectscheme.scheme=https"
      - "traefik.http.middlewares.myapp-redirect.redirectscheme.permanent=true"
      # Puerto interno
      - "traefik.http.services.myapp-api.loadbalancer.server.port=3000"

  # ════════════════════════════════════════════════════════
  # SERVICIO PÚBLICO: FRONTEND (con labels de Traefik)
  # ════════════════════════════════════════════════════════
  web:
    image: node:18.20.1-alpine3.18
    container_name: myapp_web
    restart: unless-stopped
    expose:
      - "8080"
    environment:
      - REACT_APP_API_URL=https://app.da-tica.com/api
      - PORT=8080
    depends_on:
      api:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://127.0.0.1:8080/"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s
    networks:
      - coolify
      - default
    labels:
      - "traefik.enable=true"
      # Router HTTPS (principal)
      - "traefik.http.routers.myapp-web.rule=Host(`app.da-tica.com`)"
      - "traefik.http.routers.myapp-web.entrypoints=websecure"
      - "traefik.http.routers.myapp-web.tls.certresolver=letsencrypt"
      - "traefik.http.routers.myapp-web.middlewares=myapp-redirect"
      # Router HTTP (solo redirect)
      - "traefik.http.routers.myapp-web-http.rule=Host(`app.da-tica.com`)"
      - "traefik.http.routers.myapp-web-http.entrypoints=web"
      - "traefik.http.routers.myapp-web-http.middlewares=myapp-redirect"
      # Middleware redirect HTTP → HTTPS
      - "traefik.http.middlewares.myapp-redirect.redirectscheme.scheme=https"
      - "traefik.http.middlewares.myapp-redirect.redirectscheme.permanent=true"
      # Puerto interno
      - "traefik.http.services.myapp-web.loadbalancer.server.port=8080"

  # ════════════════════════════════════════════════════════
  # SERVICIOS INTERNOS (sin labels, sin red coolify)
  # ════════════════════════════════════════════════════════
  postgres:
    image: postgres:16-alpine
    container_name: myapp_postgres
    restart: unless-stopped
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=${DB_PASSWORD}
      - POSTGRES_DB=myapp_db
    volumes:
      - postgres_data:/var/lib/postgresql/data
    expose:
      - "5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s
    networks:
      - default

  redis:
    image: redis:7-alpine
    container_name: myapp_redis
    restart: unless-stopped
    expose:
      - "6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 3
    networks:
      - default

# ════════════════════════════════════════════════════════
# REDES
# ════════════════════════════════════════════════════════
networks:
  coolify:
    external: true
  default:
    driver: bridge

# ════════════════════════════════════════════════════════
# VOLÚMENES PERSISTENTES
# ════════════════════════════════════════════════════════
volumes:
  postgres_data:
  redis_data:
  app_logs:

#
# ═══════════════════════════════════════════════════════════════════
# POST-DEPLOY: PASOS OBLIGATORIOS EN COOLIFY
# ═══════════════════════════════════════════════════════════════════
#
# 1. DESACTIVAR AUTO-GENERACIÓN DE LABELS
#    En Coolify UI: Proyecto → Configuración
#    "Auto-generate Traefik labels" → DESACTIVAR (OFF)
#    Motivo: Ya están declarados en docker-compose.yml
#
# 2. VARIABLES DE ENTORNO A CONFIGURAR EN COOLIFY
#    DATABASE_URL=postgres://postgres:${DB_PASSWORD}@postgres:5432/myapp_db
#    REDIS_URL=redis://redis:6379
#    API_KEY=tu_api_key_aqui
#    JWT_SECRET=tu_jwt_secret_aqui
#    DB_PASSWORD=contraseña_segura_aqui
#
# 3. PUERTOS INTERNOS DETECTADOS POR TRAEFIK
#    API: 3000 (debe coincidir con expose y traefik label)
#    Web: 8080 (debe coincidir con expose y traefik label)
#
# 4. VERIFICACIONES PRE-DEPLOY
#    a) Node.js escucha en 0.0.0.0:3000 y 0.0.0.0:8080
#       (no solo localhost)
#    b) Healthcheck endpoints:
#       API: GET /health → 200 OK
#       Web: GET / → 200 OK
#    c) Servicios internos accesibles:
#       - postgres:5432 (red default)
#       - redis:6379 (red default)
#
# 5. DNS EN CLOUDFLARE
#    app.da-tica.com → A record → 217.216.81.73
#    Proxy: Activado (naranja) en Cloudflare
#
# 6. VERIFICACIÓN POST-DEPLOY
#    Ejecutar en terminal:
#    curl -I https://app.da-tica.com
#    → Debe responder 301 (redirect a /), 200 OK, NO 502/503
#
#    curl -I https://app.da-tica.com/api/health
#    → Debe responder 200 OK
#
# 7. CERTIFICADO LETS ENCRYPT
#    Traefik automáticamente obtiene certificado
#    Verificar en navegador: 🔒 HTTPS válido (candado verde)
#
# 8. MONITOREO POST-DEPLOY
#    docker ps
#    → api: Running, healthy
#    → web: Running, healthy
#    → postgres: Running, healthy
#    → redis: Running, healthy
#
```

---

## Cómo Usar Este Skill

### Opción A: Pedir compose completo

**Tú:**
> "Genera docker-compose para Node.js API (puerto 3000) + PostgreSQL + Redis. Stack: 'backend', dominio: 'api.da-tica.com'"

**Yo:**
1. Recopilo info (servicios, puertos, versiones)
2. Genero compose completo con labels ✅
3. Valido con `validate-traefik-labels` ✅
4. Te presento el resultado documentado

---

### Opción B: Mejorar compose existente

**Tú:**
> "Tengo un docker-compose.yml. Agrega labels de Traefik automáticamente."

**Yo:**
1. Leo tu compose
2. Identifico servicios públicos
3. Agrego labels con `add-traefik-labels-coolify` ✅
4. Valido resultado con `validate-traefik-labels` ✅
5. Te presento el mejorado

---

## Reglas que Automáticamente Implemento

✅ **Red coolify:**
- Servicios públicos en red `coolify` + `default`
- Servicios internos SOLO en `default`
- Red coolify = `external: true` (existe en Coolify)

✅ **Labels de Traefik:**
- Prefijo único por stack/servicio
- Router HTTPS (principal) con TLS
- Router HTTP (solo redirect)
- Middleware HTTP→HTTPS
- Puerto correcto en loadbalancer

✅ **Puertos:**
- Nunca `ports:` en servicios públicos
- Siempre `expose:` para documentación
- Puerto coincide entre expose, label y proceso

✅ **Imágenes:**
- Versiones fijas (nunca `:latest`)
- Alpine cuando esté disponible
- Tags semánticos (`postgres:16-alpine`)

✅ **Healthchecks:**
- Obligatorios en TODOS los servicios
- Binarios que existen en imagen
- `start_period` para apps lentas

✅ **Volúmenes:**
- Volúmenes nombrados (no bind mounts)
- Solo para datos persistentes
- Documentados y coherentes

✅ **Variables:**
- Sensibles = `${VARIABLE}`
- No-sensibles = inline
- Documentadas en post-deploy

---

## Integración con Otros Skills

Automáticamente:
1. Genero el compose con este skill
2. Valido labels con `validate-traefik-labels` ✅
3. Valido Coolify con `validate-coolify-compose` ✅
4. Busco errores con `validate-common-coolify-errors` ✅

Si hay problema, te muestro exactamente qué está mal.

---

## Casos Especiales

### Múltiples Dominios

Si tienes API en `api.da-tica.com` y web en `web.da-tica.com`:

```yaml
api:
  labels:
    - "traefik.http.routers.myapp-api.rule=Host(`api.da-tica.com`)"
    
web:
  labels:
    - "traefik.http.routers.myapp-web.rule=Host(`web.da-tica.com`)"
```

Automáticamente genero labels distintos para cada dominio.

---

### PathPrefix (API + Frontend mismo dominio)

Si ambos en `app.da-tica.com`:
- API: `/api/*` → puerto 3000
- Frontend: `/` → puerto 8080

```yaml
api:
  labels:
    - "traefik.http.routers.myapp-api.rule=Host(`app.da-tica.com`) && PathPrefix(`/api`)"
    
web:
  labels:
    - "traefik.http.routers.myapp-web.rule=Host(`app.da-tica.com`) && !PathPrefix(`/api`)"
```

Automáticamente genero reglas de routing correctas.

---

## Recuerda

Este skill genera **todo automáticamente**. No necesitas tocar labels manualmente en la UI de Coolify. Solo:

1. Copia el compose generado
2. Configura variables en Coolify UI
3. Desactiva auto-generación de labels
4. Push a GitHub → Coolify deploya

**Sin sorpresas. Todo funciona a la primera.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
