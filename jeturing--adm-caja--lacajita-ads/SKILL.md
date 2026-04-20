---
name: lacajita-ads
description: Skill para el sistema de anuncios y publicidad de La Cajita TV. Usar cuando se trabaje con ad requests, campañas publicitarias, agencias, facturación de anuncios, o tablas relacionadas con ads/advertising. Use when this capability is needed.
metadata:
  author: jeturing
---

# La Cajita TV - Advertising Skill

Guía para gestión del sistema de anuncios y publicidad.

## Arquitectura del Sistema de Ads

```
Agencia                    Admin
   │                         │
   │ Crea solicitud          │ Aprueba/Rechaza
   ▼                         ▼
ad_requests ────────────► Revisión
   │
   │ Aprobado
   ▼
ad_campaigns ────────────► Activa
   │
   │ Impresiones/Clicks
   ▼
ad_metrics ──────────────► Reportes
   │
   │ Fin de campaña
   ▼
invoices ────────────────► Facturación
```

## Base de Datos

### Tabla: ad_requests

```sql
CREATE TABLE ad_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  agency_id UUID REFERENCES users(id),
  title VARCHAR(255) NOT NULL,
  description TEXT,
  ad_type VARCHAR(50) NOT NULL,        -- banner, video, overlay
  target_audience JSONB,               -- Segmentación
  budget DECIMAL(10,2),
  start_date DATE,
  end_date DATE,
  status VARCHAR(50) DEFAULT 'pending', -- pending, approved, rejected, active
  media_url TEXT,                       -- URL del contenido publicitario
  click_url TEXT,                       -- URL de destino
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  reviewed_by UUID REFERENCES users(id),
  reviewed_at TIMESTAMPTZ
);

-- Estados del flujo:
-- pending → approved → active → completed
-- pending → rejected
```

### Tabla: ad_campaigns

```sql
CREATE TABLE ad_campaigns (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  request_id UUID REFERENCES ad_requests(id),
  agency_id UUID REFERENCES users(id),
  name VARCHAR(255) NOT NULL,
  ad_type VARCHAR(50) NOT NULL,
  media_url TEXT NOT NULL,
  click_url TEXT,
  impression_goal INT,
  click_goal INT,
  daily_budget DECIMAL(10,2),
  total_budget DECIMAL(10,2),
  cpm_rate DECIMAL(6,2),               -- Costo por mil impresiones
  cpc_rate DECIMAL(6,2),               -- Costo por click
  start_date DATE NOT NULL,
  end_date DATE NOT NULL,
  status VARCHAR(50) DEFAULT 'active',
  targeting JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### Tabla: ad_metrics

```sql
CREATE TABLE ad_metrics (
  id SERIAL PRIMARY KEY,
  campaign_id UUID REFERENCES ad_campaigns(id),
  date DATE NOT NULL,
  impressions INT DEFAULT 0,
  clicks INT DEFAULT 0,
  cost DECIMAL(10,2) DEFAULT 0,
  unique_viewers INT DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(campaign_id, date)
);
```

### Tabla: agencies

```sql
CREATE TABLE agencies (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  company_name VARCHAR(255) NOT NULL,
  contact_email VARCHAR(255),
  contact_phone VARCHAR(50),
  billing_address TEXT,
  tax_id VARCHAR(50),
  credit_limit DECIMAL(12,2) DEFAULT 0,
  balance DECIMAL(12,2) DEFAULT 0,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

## API Endpoints

### Para Agencias

```python
# Crear solicitud de anuncio
POST /api/ads/requests
{
  "title": "Campaña Verano 2026",
  "description": "Promoción productos de verano",
  "ad_type": "video",
  "budget": 5000.00,
  "start_date": "2026-03-01",
  "end_date": "2026-03-31",
  "media_url": "https://cdn.example.com/ad_video.mp4",
  "click_url": "https://tienda.example.com/verano"
}

# Listar mis solicitudes
GET /api/ads/requests?status=pending

# Ver métricas de mis campañas
GET /api/ads/campaigns/{id}/metrics
```

### Para Admin

```python
# Listar solicitudes pendientes
GET /api/admin/ads/requests?status=pending

# Aprobar solicitud
POST /api/admin/ads/requests/{id}/approve
{
  "cpm_rate": 5.00,
  "notes": "Aprobado para horario prime time"
}

# Rechazar solicitud
POST /api/admin/ads/requests/{id}/reject
{
  "reason": "Contenido no cumple políticas"
}

# Dashboard de métricas
GET /api/admin/ads/dashboard
```

## Roles y Permisos

| Rol | Permisos |
|-----|----------|
| `agency` | Crear solicitudes, ver propias campañas, ver métricas propias |
| `admin` | Aprobar/rechazar, gestionar todas las campañas, ver todos los reportes |
| `producer` | Sin acceso al sistema de ads |
| `user` | Sin acceso |

## Tipos de Anuncios

| Tipo | Descripción | Formato |
|------|-------------|---------|
| `banner` | Imagen estática o animada | PNG, GIF, 728x90, 300x250 |
| `video` | Video pre-roll o mid-roll | MP4, max 30s |
| `overlay` | Superposición sobre contenido | PNG transparente |
| `sponsored` | Contenido patrocinado | Playlist destacada |

## Facturación

### Tabla: invoices

```sql
CREATE TABLE invoices (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  agency_id UUID REFERENCES agencies(id),
  invoice_number VARCHAR(50) UNIQUE,
  period_start DATE,
  period_end DATE,
  subtotal DECIMAL(12,2),
  tax DECIMAL(12,2),
  total DECIMAL(12,2),
  status VARCHAR(50) DEFAULT 'draft', -- draft, sent, paid, overdue
  due_date DATE,
  paid_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE invoice_items (
  id SERIAL PRIMARY KEY,
  invoice_id UUID REFERENCES invoices(id),
  campaign_id UUID REFERENCES ad_campaigns(id),
  description TEXT,
  quantity INT,                        -- Impresiones o clicks
  unit_price DECIMAL(10,4),           -- CPM o CPC
  amount DECIMAL(12,2)
);
```

### Generar Factura

```python
@app.post("/api/admin/invoices/generate")
@require_auth(roles=["admin"])
async def generate_invoice(agency_id: str, period_start: str, period_end: str):
    # 1. Obtener métricas del período
    # 2. Calcular costos por campaña
    # 3. Crear invoice con items
    # 4. Enviar notificación a agencia
    ...
```

## Código de Ejemplo

### Crear Ad Request

```python
@app.post("/api/ads/requests")
@require_auth(roles=["agency"])
async def create_ad_request(request: Request, data: AdRequestCreate):
    user_id = request.state.user.get("sub")
    
    with get_db_connection() as conn:
        cursor = conn.cursor()
        cursor.execute("""
            INSERT INTO ad_requests 
            (agency_id, title, description, ad_type, budget, start_date, end_date, media_url, click_url)
            VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s)
            RETURNING id
        """, (user_id, data.title, data.description, data.ad_type, 
              data.budget, data.start_date, data.end_date, data.media_url, data.click_url))
        
        request_id = cursor.fetchone()[0]
        conn.commit()
    
    # Notificar a admins
    await notify_admins_new_ad_request(request_id)
    
    return {"id": str(request_id), "status": "pending"}
```

### Registrar Impresión

```python
@app.post("/api/ads/impression")
async def track_impression(campaign_id: str, viewer_id: str = None):
    today = date.today()
    
    with get_db_connection() as conn:
        cursor = conn.cursor()
        cursor.execute("""
            INSERT INTO ad_metrics (campaign_id, date, impressions)
            VALUES (%s, %s, 1)
            ON CONFLICT (campaign_id, date) 
            DO UPDATE SET impressions = ad_metrics.impressions + 1
        """, (campaign_id, today))
        conn.commit()
    
    return {"recorded": True}
```

## Documentación Relacionada

Ver `/opt/adm-caja-unified/docs/modules/ads/AD_MANAGEMENT_SYSTEM.md` para documentación completa.

## Checklist de Ads

- [ ] Solicitud creada con todos los campos requeridos
- [ ] Media URL accesible y formato correcto
- [ ] Presupuesto y fechas válidas
- [ ] Segmentación configurada (opcional)
- [ ] Aprobación de admin antes de activar
- [ ] Métricas registrándose correctamente
- [ ] Facturación generada al final del período

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeturing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
