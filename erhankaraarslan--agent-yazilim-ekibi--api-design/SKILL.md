---
name: api-design
description: RESTful API tasarım standartları. API TASARIMI veya ENDPOINT GELİŞTİRME yaparken otomatik uygula. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# API Design Standards

## URL Yapısı

| Kural | Doğru | Yanlış |
|-------|-------|--------|
| Çoğul isim | `/users` | `/user` |
| Kebab-case | `/user-profiles` | `/userProfiles` |
| Lowercase | `/orders` | `/Orders` |
| İsim kullan | `/users` | `/getUsers` |

## Resource Hierarchy

```
GET    /users                  # Liste
POST   /users                  # Oluştur
GET    /users/{id}             # Tekil
PUT    /users/{id}             # Güncelle (tam)
PATCH  /users/{id}             # Güncelle (kısmi)
DELETE /users/{id}             # Sil

# Nested
GET    /users/{id}/orders      # User'ın order'ları
```

## HTTP Methods

| Method | Kullanım | Idempotent |
|--------|----------|------------|
| GET | Oku | ✅ |
| POST | Oluştur | ❌ |
| PUT | Tam güncelle | ✅ |
| PATCH | Kısmi güncelle | ❌ |
| DELETE | Sil | ✅ |

## Status Codes

### Success
| Code | Kullanım |
|------|----------|
| 200 | GET/PUT/PATCH başarılı |
| 201 | POST created |
| 204 | DELETE no content |

### Client Error
| Code | Kullanım |
|------|----------|
| 400 | Validation error |
| 401 | Auth gerekli |
| 403 | Yetkisiz |
| 404 | Bulunamadı |
| 409 | Conflict |
| 429 | Rate limit |

### Server Error
| Code | Kullanım |
|------|----------|
| 500 | Internal error |

## Response Format

### Success
```json
{
  "success": true,
  "data": { "id": "usr_123", "email": "..." },
  "meta": { "page": 1, "total": 100 }
}
```

### Error
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [{ "field": "email", "message": "Invalid" }]
  }
}
```

## Pagination

```
GET /users?page=2&limit=20
```

```json
{
  "data": [...],
  "meta": {
    "page": 2,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}
```

## Filtering & Sorting

```
GET /users?status=active
GET /users?role=admin&status=active
GET /users?sort=-createdAt       # DESC
GET /users?sort=name             # ASC
GET /users?search=john
```

## Auth Endpoints

```
POST /auth/register
POST /auth/login        → token döner
POST /auth/logout
POST /auth/refresh
GET  /auth/me
```

## Headers

```
Content-Type: application/json
Authorization: Bearer <token>
Accept: application/json
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
