---
name: validate-traefik-labels
description: Valida que las labels de Traefik/Coolify estén correctamente configuradas en docker-compose, incluyendo rutas, dominios, puertos, middlewares y reglas de redirect HTTP a HTTPS. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Validar Labels de Traefik para Coolify

## Propósito

Verificar que un `docker-compose.yml` tiene **todas las labels de Traefik necesarias** y correctamente configuradas para funcionar en Coolify sin intervención manual en la UI.

---

## Reglas de Labels Obligatorias

### 1. Estructura de Nombres (Prefijo)

Los nombres de routers, middlewares y servicios deben seguir: `{stack}-{servicio}`

```yaml
# ✅ CORRECTO
traefik.http.routers.portal-web.rule=Host(`portal.da-tica.com`)
traefik.http.routers.api-app.rule=Host(`api.da-tica.com`)

# ❌ INCORRECTO (nombres inconsistentes)
traefik.http.routers.web.rule=Host(`portal.da-tica.com`)
traefik.http.routers.app_service.rule=Host(`api.da-tica.com`)
```

### 2. Labels Obligatorias por Servicio Público

Todo servicio web expuesto debe tener:

```yaml
labels:
  # Habilitar Traefik
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

### 3. Red Coolify

```yaml
# ✅ CORRECTO
services:
  web:
    networks:
      - coolify
      - default  # si necesita hablar con DB/cache
    labels:
      - "traefik.enable=true"
      # ... labels ...

networks:
  coolify:
    external: true
```

### 4. Puertos

```yaml
# ✅ CORRECTO
services:
  web:
    expose:
      - "8080"
    labels:
      - "traefik.http.services.{stack}-web.loadbalancer.server.port=8080"
    # NO USAR ports:

# ❌ INCORRECTO
services:
  web:
    ports:
      - "8080:8080"  # Traefik no puede enrutar correctamente
```

### 5. Coincidencia de Puertos

El puerto en `expose`, en el label `loadbalancer.server.port` y el puerto real en el contenedor deben ser **idénticos**.

```yaml
# ✅ CORRECTO
services:
  app:
    image: node:18-alpine
    expose:
      - "3000"
    environment:
      - PORT=3000
    labels:
      - "traefik.http.services.mystack-app.loadbalancer.server.port=3000"

# ❌ INCORRECTO (puertos diferentes)
services:
  app:
    image: node:18-alpine
    expose:
      - "8080"  # aquí dice 8080
    environment:
      - PORT=3000  # pero app escucha en 3000
    labels:
      - "traefik.http.services.mystack-app.loadbalancer.server.port=8080"  # inconsistencia
```

---

## Checklist de Validación

Para cada servicio público (con labels):

### Paso 1: Verificar Nombre de Labels
- [ ] ✅ `traefik.enable=true` presente
- [ ] ✅ `traefik.http.routers.{stack}-{servicio}.rule=Host(...)`
- [ ] ✅ Prefijo `{stack}-{servicio}` es consistente en TODAS las labels

### Paso 2: Verificar Routers HTTPS
- [ ] ✅ Router HTTPS está definido (websecure entrypoint)
- [ ] ✅ `tls.certresolver=letsencrypt` presente
- [ ] ✅ Dominio en `rule=Host(...)` es correcto
- [ ] ✅ Middleware redirect está referenciado

### Paso 3: Verificar Router HTTP → HTTPS
- [ ] ✅ Router HTTP existe con sufijo `-http`
- [ ] ✅ Entrypoint es `web`
- [ ] ✅ Middleware redirect está referenciado

### Paso 4: Verificar Middleware Redirect
- [ ] ✅ Middleware `{stack}-redirect` está definida
- [ ] ✅ `redirectscheme.scheme=https`
- [ ] ✅ `redirectscheme.permanent=true`

### Paso 5: Verificar Puerto
- [ ] ✅ `expose: ["puerto"]` definido
- [ ] ✅ `loadbalancer.server.port={puerto}` coincide con expose
- [ ] ✅ Puerto coincide con puerto real de la aplicación

### Paso 6: Verificar Red
- [ ] ✅ Red `coolify` declarada como `external: true`
- [ ] ✅ Servicio público está en red `coolify`
- [ ] ✅ Si hay servicios internos, están en red `default`

### Paso 7: Verificar Servicios Internos
- [ ] ✅ BD, cache, workers NO tienen `labels`
- [ ] ✅ BD, cache, workers NO están en red `coolify`
- [ ] ✅ Comunicación interna por nombre de servicio (ej: `db:5432`)

---

## Ejemplos de Validación

### Ejemplo 1: ✅ CORRECTO

```yaml
version: "3.8"

services:
  web:
    image: nginx:1.27-alpine
    expose:
      - "80"
    networks:
      - coolify
      - default
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
      - "traefik.http.services.portal-web.loadbalancer.server.port=80"
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://127.0.0.1:80/"]
      interval: 30s
      timeout: 5s
      retries: 3

  db:
    image: postgres:16-alpine
    expose:
      - "5432"
    networks:
      - default  # ✅ SIN coolify
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

networks:
  coolify:
    external: true
  default:

volumes:
  postgres_data:
```

**Validación:** ✅ PASA TODOS LOS CHECKS

---

### Ejemplo 2: ❌ INCORRECTO

```yaml
version: "3.8"

services:
  web:
    image: nginx:latest  # ❌ sin versión
    ports:
      - "80:80"  # ❌ NO debe tener ports
    expose:
      - "8080"  # ❌ no coincide con ports
    networks:
      - default  # ❌ NO está en coolify
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.web.rule=Host(`portal.da-tica.com`)"  # ❌ prefijo incorrecto
      # ❌ FALTAN labels críticas:
      # - entrypoints
      # - tls.certresolver
      # - middleware redirect
      # - router http
      - "traefik.http.services.web.loadbalancer.server.port=8080"  # ❌ puerto no coincide

  db:
    image: postgres:latest  # ❌ sin versión
    ports:
      - "5432:5432"  # ❌ DB nunca debe tener ports
    networks:
      - coolify  # ❌ servicios internos NO en coolify
    labels:  # ❌ servicios internos NO llevan labels
      - "traefik.enable=false"

# ❌ FALTA network coolify: external: true
```

**Validación:** ❌ FALLA 10+ CHECKS

---

## Reporte de Validación

Cuando valides un compose, reporta así:

```
## ✅ VALIDACIÓN DE LABELS TRAEFIK

### Servicio: web
- [✅] traefik.enable=true
- [✅] Router HTTPS configurado correctamente
- [✅] Router HTTP con redirect
- [✅] Middleware redirect definida
- [✅] Puerto coincide (expose=8080, loadbalancer=8080)
- [✅] Red coolify configurada
- [✅] Imagen con versión fija (nginx:1.27-alpine)

### Servicio: api
- [✅] Labels completas
- [✅] Dominio correcto (api.da-tica.com)
- [✅] Puerto 3000 consistente

### Servicio: db
- [✅] NO tiene labels (correcto para servicio interno)
- [✅] NO está en red coolify (correcto)
- [✅] BD NO expuesta por puerto (correcto)

### Redes
- [✅] Network coolify es external: true
- [✅] Servicios internos en red default

### RESULTADO: ✅ LISTO PARA COOLIFY
```

---

## Errores Comunes y Cómo Detectarlos

### Error #1: Labels Incompletas

**Síntoma:** Servicio inaccesible por dominio

**Detección:**
```bash
# Buscar si faltan labels críticas
grep "traefik.http.routers" docker-compose.yml | wc -l
# Si es menor a 6 por servicio, faltan labels
```

**Validación:** Checklist Paso 2, 3, 4

---

### Error #2: Puertos Inconsistentes

**Síntoma:** "Connection refused" desde Traefik

**Detección:**
```bash
# Extraer puertos
grep "expose:" docker-compose.yml
grep "loadbalancer.server.port=" docker-compose.yml
# Deben coincidir
```

**Validación:** Checklist Paso 5

---

### Error #3: Servicio en Red Equivocada

**Síntoma:** Traefik no ve el servicio

**Detección:**
```bash
# Verificar red coolify
grep -A 5 "networks:" docker-compose.yml | grep "coolify"
```

**Validación:** Checklist Paso 6

---

### Error #4: Prefijo de Labels Inconsistente

**Síntoma:** Traefik ve routers/middlewares pero no conectan

**Detección:**
```bash
# Extraer prefijos de todos los routers
grep "traefik.http.routers" docker-compose.yml | grep -o "{[^}]*}" | sort -u
# Deben ser iguales dentro del mismo stack
```

**Validación:** Checklist Paso 1

---

## Comandos de Diagnóstico Rápido

```bash
# 1. Validar sintaxis YAML
docker-compose config > /dev/null && echo "✅ YAML válido"

# 2. Contar labels de Traefik
docker-compose config | grep -c "traefik.enable"

# 3. Verificar puertos coinciden
echo "expose:"
docker-compose config | grep -A 1 "expose:"
echo "loadbalancer.server.port:"
docker-compose config | grep "loadbalancer.server.port"

# 4. Verificar redes
docker-compose config | grep -A 10 "networks:"

# 5. Listar servicios en coolify
docker-compose config | grep -B 10 "coolify" | grep "^    [a-z]"
```

---

## Checklist Final

Antes de decir "✅ LISTO PARA COOLIFY":

- [ ] ✅ Todos los servicios públicos tienen labels completas
- [ ] ✅ Prefijo `{stack}-{servicio}` es consistente
- [ ] ✅ Puertos en expose, loadbalancer y app coinciden
- [ ] ✅ Red coolify es `external: true`
- [ ] ✅ Servicios públicos en red coolify
- [ ] ✅ Servicios internos NO en red coolify
- [ ] ✅ NO hay `ports:` en servicios públicos (solo expose)
- [ ] ✅ Router HTTPS con certresolver=letsencrypt
- [ ] ✅ Router HTTP con redirect a HTTPS
- [ ] ✅ Middleware redirect definida
- [ ] ✅ Servicios internos NO tienen labels
- [ ] ✅ Imágenes tienen versiones fijas
- [ ] ✅ Healthchecks presentes

---

## Integración con Otros Skills

- **Después de generar compose:** usa este skill para validar labels
- **Si hay errores:** usa `add-traefik-labels-coolify` para agregarlas automáticamente
- **Antes de push:** incluir en `coolify-deploy-checklist`

```
</parameter>
</invoke>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
