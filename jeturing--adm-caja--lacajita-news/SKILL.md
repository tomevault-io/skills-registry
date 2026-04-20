---
name: lacajita-news
description: Skill para el sistema de noticias de La Cajita TV. Usar cuando se trabaje con news feed, artículos, interacciones de usuarios (likes, comentarios), videos de noticias, o tablas news_*. Use when this capability is needed.
metadata:
  author: jeturing
---

# La Cajita TV - News System Skill

Guía para gestión del sistema de noticias y contenido editorial.

## Arquitectura del Sistema de Noticias

```
Producer/Admin
      │
      │ Crea contenido
      ▼
 news_feed ──────────► Publicación
      │
      │ Usuario interactúa
      ├──────────────────────┐
      ▼                      ▼
news_likes              news_comments
      │                      │
      └──────────┬───────────┘
                 ▼
           news_metrics
                 │
                 ▼
            Analytics
```

## Base de Datos

### Tabla: news_feed

```sql
CREATE TABLE news_feed (
  id SERIAL PRIMARY KEY,
  title VARCHAR(500) NOT NULL,
  slug VARCHAR(500) UNIQUE,
  content TEXT,
  excerpt TEXT,                        -- Resumen corto
  image_url TEXT,                      -- Imagen principal
  video_url TEXT,                      -- Video asociado (opcional)
  author_id UUID REFERENCES users(id),
  category VARCHAR(100),
  tags TEXT[],                         -- Array de tags
  is_published BOOLEAN DEFAULT false,
  is_featured BOOLEAN DEFAULT false,
  published_at TIMESTAMPTZ,
  view_count INT DEFAULT 0,
  like_count INT DEFAULT 0,
  comment_count INT DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_news_published ON news_feed(is_published, published_at DESC);
CREATE INDEX idx_news_category ON news_feed(category);
CREATE INDEX idx_news_author ON news_feed(author_id);
```

### Tabla: news_likes

```sql
CREATE TABLE news_likes (
  id SERIAL PRIMARY KEY,
  news_id INT REFERENCES news_feed(id) ON DELETE CASCADE,
  user_id UUID,                        -- Puede ser anónimo (device_id)
  device_id VARCHAR(100),
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(news_id, user_id),
  UNIQUE(news_id, device_id)
);
```

### Tabla: news_comments

```sql
CREATE TABLE news_comments (
  id SERIAL PRIMARY KEY,
  news_id INT REFERENCES news_feed(id) ON DELETE CASCADE,
  user_id UUID REFERENCES users(id),
  content TEXT NOT NULL,
  parent_id INT REFERENCES news_comments(id), -- Para respuestas
  is_approved BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### Tabla: news_videos

```sql
CREATE TABLE news_videos (
  id SERIAL PRIMARY KEY,
  news_id INT REFERENCES news_feed(id),
  video_url TEXT NOT NULL,
  thumbnail_url TEXT,
  duration_seconds INT,
  provider VARCHAR(50),               -- jwplayer, youtube, vimeo
  provider_id VARCHAR(100),
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

## API Endpoints

### Públicos (Mobile App)

```python
# Feed de noticias
GET /api/news/feed?page=1&limit=20
GET /api/news/feed?category=deportes

# Detalle de noticia
GET /api/news/{id}
GET /api/news/slug/{slug}

# Noticias destacadas
GET /api/news/featured

# Categorías
GET /api/news/categories

# Buscar
GET /api/news/search?q=texto
```

### Interacciones (Requiere Auth o Device ID)

```python
# Like
POST /api/news/{id}/like
DELETE /api/news/{id}/like

# Comentarios
GET /api/news/{id}/comments
POST /api/news/{id}/comments
DELETE /api/news/comments/{comment_id}

# Compartir (tracking)
POST /api/news/{id}/share
```

### Admin/Producer

```python
# CRUD de noticias
POST /api/admin/news
PUT /api/admin/news/{id}
DELETE /api/admin/news/{id}

# Publicar/Despublicar
POST /api/admin/news/{id}/publish
POST /api/admin/news/{id}/unpublish

# Moderar comentarios
GET /api/admin/news/comments/pending
POST /api/admin/news/comments/{id}/approve
DELETE /api/admin/news/comments/{id}
```

## Código de Ejemplo

### Obtener Feed

```python
@app.get("/api/news/feed")
@app.get("/news/feed")
async def get_news_feed(
    page: int = 1,
    limit: int = 20,
    category: str = None
):
    offset = (page - 1) * limit
    
    query = """
        SELECT n.id, n.title, n.slug, n.excerpt, n.image_url,
               n.category, n.published_at, n.view_count, n.like_count,
               u.name as author_name
        FROM news_feed n
        LEFT JOIN users u ON n.author_id = u.id
        WHERE n.is_published = true
    """
    params = []
    
    if category:
        query += " AND n.category = %s"
        params.append(category)
    
    query += " ORDER BY n.published_at DESC LIMIT %s OFFSET %s"
    params.extend([limit, offset])
    
    with get_db_connection() as conn:
        cursor = conn.cursor()
        cursor.execute(query, params)
        news = cursor.fetchall()
    
    return {
        "news": [
            {
                "id": n[0],
                "title": n[1],
                "slug": n[2],
                "excerpt": n[3],
                "image_url": get_image_url(n[4]),
                "category": n[5],
                "published_at": n[6].isoformat() if n[6] else None,
                "view_count": n[7],
                "like_count": n[8],
                "author_name": n[9]
            }
            for n in news
        ],
        "page": page,
        "limit": limit
    }
```

### Toggle Like

```python
@app.post("/api/news/{news_id}/like")
async def toggle_like(news_id: int, request: Request):
    # Obtener user_id o device_id
    user_id = getattr(request.state, 'user', {}).get('sub')
    device_id = request.headers.get('X-Device-ID')
    
    if not user_id and not device_id:
        raise HTTPException(400, "Se requiere autenticación o device ID")
    
    with get_db_connection() as conn:
        cursor = conn.cursor()
        
        # Verificar si ya existe like
        if user_id:
            cursor.execute(
                "SELECT id FROM news_likes WHERE news_id = %s AND user_id = %s",
                (news_id, user_id)
            )
        else:
            cursor.execute(
                "SELECT id FROM news_likes WHERE news_id = %s AND device_id = %s",
                (news_id, device_id)
            )
        
        existing = cursor.fetchone()
        
        if existing:
            # Quitar like
            cursor.execute("DELETE FROM news_likes WHERE id = %s", (existing[0],))
            cursor.execute(
                "UPDATE news_feed SET like_count = like_count - 1 WHERE id = %s",
                (news_id,)
            )
            liked = False
        else:
            # Agregar like
            cursor.execute(
                "INSERT INTO news_likes (news_id, user_id, device_id) VALUES (%s, %s, %s)",
                (news_id, user_id, device_id)
            )
            cursor.execute(
                "UPDATE news_feed SET like_count = like_count + 1 WHERE id = %s",
                (news_id,)
            )
            liked = True
        
        conn.commit()
    
    return {"liked": liked}
```

### Crear Noticia (Admin)

```python
@app.post("/api/admin/news")
@require_auth(roles=["admin", "producer"])
async def create_news(request: Request, data: NewsCreate):
    author_id = request.state.user.get("sub")
    
    # Generar slug
    slug = slugify(data.title)
    
    with get_db_connection() as conn:
        cursor = conn.cursor()
        cursor.execute("""
            INSERT INTO news_feed 
            (title, slug, content, excerpt, image_url, author_id, category, tags)
            VALUES (%s, %s, %s, %s, %s, %s, %s, %s)
            RETURNING id
        """, (data.title, slug, data.content, data.excerpt, 
              data.image_url, author_id, data.category, data.tags))
        
        news_id = cursor.fetchone()[0]
        conn.commit()
    
    return {"id": news_id, "slug": slug}
```

## Roles y Permisos

| Rol | Permisos |
|-----|----------|
| `admin` | CRUD completo, moderar comentarios, publicar cualquier noticia |
| `producer` | Crear/editar propias noticias, publicar propias |
| `agency` | Solo lectura |
| `user` | Lectura, likes, comentarios |

## Categorías Estándar

- Deportes
- Noticias
- Entretenimiento
- Tecnología
- Salud
- Internacional
- Local

## Métricas

```sql
-- Noticias más vistas hoy
SELECT id, title, view_count
FROM news_feed
WHERE is_published = true
  AND published_at >= CURRENT_DATE
ORDER BY view_count DESC
LIMIT 10;

-- Engagement por categoría
SELECT 
  category,
  COUNT(*) as total_news,
  SUM(view_count) as total_views,
  SUM(like_count) as total_likes,
  SUM(comment_count) as total_comments
FROM news_feed
WHERE is_published = true
GROUP BY category;
```

## Checklist de Noticias

- [ ] Título y contenido completos
- [ ] Imagen principal subida y optimizada
- [ ] Categoría asignada
- [ ] Slug generado correctamente
- [ ] Excerpt (resumen) creado
- [ ] Revisión antes de publicar
- [ ] Notificación push enviada (si destacada)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeturing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
