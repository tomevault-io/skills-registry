---
name: lacajita-database
description: Skill para gestión de base de datos PostgreSQL de La Cajita TV. Usar cuando se trabaje con esquemas, migraciones SQL, queries, índices, o archivos en /db-migration/. Use when this capability is needed.
metadata:
  author: jeturing
---

# La Cajita TV - Database Skill

Guía para gestión de PostgreSQL en La Cajita TV.

## Conexión

```bash
# Conexión local
psql -h localhost -U lacajita_app -d lacajita_db

# Variables de entorno
PG_HOST=localhost
PG_PORT=5432
PG_DB=lacajita_db
PG_USER=lacajita_app
```

## Esquema Principal

### Tablas de Contenido

```sql
-- Playlists
CREATE TABLE lacajita_playlists (
  id TEXT PRIMARY KEY,
  segment_id INT,
  title TEXT NOT NULL,
  description TEXT,
  category TEXT,
  img TEXT,                    -- URL de imagen
  active INT DEFAULT 1,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Canales LiveTV
CREATE TABLE livetv_channels (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(200) NOT NULL,
  stream_url TEXT NOT NULL,
  logo_url TEXT,               -- Logo principal
  logo_thumbnail TEXT,         -- Miniatura
  quality VARCHAR(20) DEFAULT 'HD',
  category VARCHAR(100),
  is_active BOOLEAN DEFAULT true,
  display_order INT DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Noticias
CREATE TABLE news_feed (
  id SERIAL PRIMARY KEY,
  title VARCHAR(500) NOT NULL,
  content TEXT,
  image_url TEXT,
  author_id UUID REFERENCES users(id),
  published_at TIMESTAMPTZ,
  is_published BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### Tablas de Usuarios

```sql
-- Usuarios
CREATE TABLE users (
  id UUID PRIMARY KEY,
  auth0_id TEXT UNIQUE NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255),
  role VARCHAR(50) DEFAULT 'user',
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Roles: admin, producer, agency, user
```

### Tablas de Anuncios

```sql
-- Solicitudes de anuncios
CREATE TABLE ad_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  agency_id UUID REFERENCES users(id),
  title VARCHAR(255) NOT NULL,
  description TEXT,
  budget DECIMAL(10,2),
  status VARCHAR(50) DEFAULT 'pending',
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Estados: pending, approved, rejected, active, completed
```

## Migraciones

### Ubicación

```
/opt/adm-caja-unified/db-migration/
├── schema_postgres.sql           # Esquema base
├── livetv_native_system.sql      # Sistema LiveTV
├── news_interactions.sql         # Interacciones noticias
├── ads_pricing_agencies.sql      # Sistema de anuncios
├── invoice_system.sql            # Facturación
└── performance_indices.sql       # Índices de rendimiento
```

### Ejecutar Migración

```bash
cd /opt/adm-caja-unified/db-migration

# Ejecutar archivo SQL
psql -h localhost -U lacajita_app -d lacajita_db -f schema_postgres.sql

# Con script Python
python3 migrate_apis_to_postgres.py
```

## Queries Comunes

### Playlists Activas

```sql
SELECT id, title, category, img
FROM lacajita_playlists
WHERE active = 1
ORDER BY created_at DESC
LIMIT 20;
```

### Canales LiveTV con Logo

```sql
SELECT 
  id, name, stream_url, logo_url, logo_thumbnail,
  quality, category
FROM livetv_channels
WHERE is_active = true
ORDER BY display_order ASC;
```

### Noticias Publicadas

```sql
SELECT 
  n.id, n.title, n.content, n.image_url,
  u.name as author_name, n.published_at
FROM news_feed n
JOIN users u ON n.author_id = u.id
WHERE n.is_published = true
ORDER BY n.published_at DESC
LIMIT 10;
```

### Estadísticas de Contenido

```sql
SELECT 
  'playlists' as type, COUNT(*) as total 
FROM lacajita_playlists WHERE active = 1
UNION ALL
SELECT 
  'channels' as type, COUNT(*) as total 
FROM livetv_channels WHERE is_active = true
UNION ALL
SELECT 
  'news' as type, COUNT(*) as total 
FROM news_feed WHERE is_published = true;
```

## Índices Recomendados

```sql
-- Índices de rendimiento
CREATE INDEX idx_playlists_active ON lacajita_playlists(active);
CREATE INDEX idx_playlists_category ON lacajita_playlists(category);
CREATE INDEX idx_channels_active ON livetv_channels(is_active);
CREATE INDEX idx_channels_order ON livetv_channels(display_order);
CREATE INDEX idx_news_published ON news_feed(is_published, published_at DESC);
CREATE INDEX idx_users_auth0 ON users(auth0_id);
```

## Backup y Restore

```bash
# Backup completo
pg_dump -h localhost -U lacajita_app -d lacajita_db > backup_$(date +%Y%m%d).sql

# Backup solo datos
pg_dump -h localhost -U lacajita_app -d lacajita_db --data-only > data_backup.sql

# Restore
psql -h localhost -U lacajita_app -d lacajita_db < backup.sql
```

## Convenciones

1. **Nombres de tablas**: snake_case, plural
2. **Primary keys**: `id` (UUID o SERIAL)
3. **Timestamps**: `created_at`, `updated_at` con TIMESTAMPTZ
4. **Booleans**: `is_active`, `is_published`
5. **Foreign keys**: `<table>_id` (ej: `user_id`, `channel_id`)

## Checklist Pre-Migración

- [ ] Backup de la base de datos
- [ ] Script SQL probado en entorno de desarrollo
- [ ] Índices incluidos si es necesario
- [ ] Rollback script preparado
- [ ] Documentación actualizada

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeturing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
