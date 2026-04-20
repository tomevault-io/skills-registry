---
name: lacajita-media
description: Skill para gestión de archivos multimedia de La Cajita TV. Usar cuando se trabaje con imágenes, videos, thumbnails, optimización de media, scripts de procesamiento, o archivos en /scripts/ y /fastapi-playlists/img/. Use when this capability is needed.
metadata:
  author: jeturing
---

# La Cajita TV - Media Management Skill

Guía para gestión y optimización de archivos multimedia.

## Arquitectura de Media

```
/opt/adm-caja-unified/fastapi-playlists/img/
├── livetv/                    # Logos de canales (17 archivos)
│   ├── *.png, *.jpg, *.svg
│   ├── thumbs/                # Thumbnails generados
│   │   ├── 32x32/
│   │   ├── 64x64/
│   │   ├── 128x128/
│   │   ├── 256x256/
│   │   └── 512x512/
│   └── .checksums.json        # Validación integridad
├── videos/                    # Posters de videos (futuro)
├── playlists/                 # Imágenes de playlists
└── .versions/                 # Backups versionados
```

## Scripts Disponibles

### 1. Descarga de Imágenes

```bash
# Descargar logos de LiveTV desde Strapi
python3 /opt/adm-caja-unified/scripts/download_livetv_images.py
python3 /opt/adm-caja-unified/scripts/download_livetv_images.py --force
```

### 2. Optimización de Imágenes

```bash
# Comprimir sin pérdida de calidad
/opt/adm-caja-unified/scripts/optimize_livetv_images.sh
/opt/adm-caja-unified/scripts/optimize_livetv_images.sh --no-backup
```

**Herramientas usadas:**
| Formato | Herramienta | Comando |
|---------|-------------|---------|
| PNG | optipng | `optipng -o5` |
| JPEG | jpegoptim | `jpegoptim --strip-all` |
| SVG | svgo | `svgo --quiet` |

### 3. Generación de Thumbnails

```bash
# Generar thumbnails en múltiples tamaños
python3 /opt/adm-caja-unified/scripts/generate_thumbnails.py --type livetv
python3 /opt/adm-caja-unified/scripts/generate_thumbnails.py --type all
python3 /opt/adm-caja-unified/scripts/generate_thumbnails.py --type videos --sizes 128 256
```

**Tamaños estándar:** 32x32, 64x64, 128x128, 256x256, 512x512

### 4. Validación de Integridad

```bash
# Validar archivos multimedia
python3 /opt/adm-caja-unified/scripts/validate_media_integrity.py --type livetv
python3 /opt/adm-caja-unified/scripts/validate_media_integrity.py --verify  # Contra checksums
python3 /opt/adm-caja-unified/scripts/validate_media_integrity.py --report  # JSON
```

**Validaciones:**
- Magic bytes (firma de formato)
- Extensión vs contenido real
- Legibilidad con Pillow
- Checksum SHA256

### 5. Versionado de Media

```bash
# Crear versión/snapshot
python3 /opt/adm-caja-unified/scripts/version_media_files.py --type livetv --commit -m "Descripción"

# Ver historial
python3 /opt/adm-caja-unified/scripts/version_media_files.py --type livetv --history

# Rollback
python3 /opt/adm-caja-unified/scripts/version_media_files.py --type livetv --rollback v1707520000

# Limpieza
python3 /opt/adm-caja-unified/scripts/version_media_files.py --cleanup --keep 5
```

## URLs de Servicio

Las imágenes se sirven desde:

```
# Producción (via Nginx)
https://caja.segrd.com/api/img/livetv/logo.png
https://caja.segrd.com/img/livetv/logo.png

# Desarrollo (via FastAPI)
http://localhost:8000/api/img/livetv/logo.png

# Thumbnails
https://caja.segrd.com/api/img/livetv/thumbs/128x128/logo.png
```

## Configuración Nginx

```nginx
location /img/ {
    alias /opt/adm-caja-unified/fastapi-playlists/img/;
    expires 30d;
    add_header Cache-Control "public, immutable";
}

location /api/img/ {
    alias /opt/adm-caja-unified/fastapi-playlists/img/;
    expires 30d;
    add_header Cache-Control "public, immutable";
}
```

## Cron Jobs

### Configuración: `/etc/cron.d/lacajita-media-sync`

```bash
# Sincronización semanal (Domingos 3 AM)
0 3 * * 0 root /opt/adm-caja-unified/scripts/sync_media_cron.sh

# Limpieza mensual de versiones (Día 1, 4 AM)
0 4 1 * * root python3 /opt/adm-caja-unified/scripts/version_media_files.py --cleanup --keep 10
```

### Script Wrapper: `sync_media_cron.sh`

Ejecuta pipeline completo:
1. Descargar imágenes desde Strapi
2. Optimizar imágenes
3. Generar thumbnails
4. Validar integridad
5. Crear versión de respaldo

## Dependencias del Sistema

```bash
# Instalar herramientas de optimización
apt-get install optipng jpegoptim pngquant imagemagick

# SVG optimization
npm install -g svgo

# Python
pip3 install Pillow requests psycopg2-binary
```

## Formatos Soportados

| Tipo | Formatos | Validación |
|------|----------|------------|
| Imágenes | PNG, JPEG, WebP, GIF, SVG, BMP | Magic bytes + Pillow |
| Videos (futuro) | MP4, WebM, AVI, MOV | Magic bytes + ffprobe |
| Audio (futuro) | MP3, AAC, WAV, FLAC | Magic bytes |

## Variables de Entorno (Portabilidad)

```bash
# Directorio base de media
MEDIA_BASE_DIR=/opt/adm-caja-unified/fastapi-playlists/img

# Tamaños de thumbnails (separados por coma)
THUMBNAIL_SIZES=32,64,128,256,512

# URL base para servir imágenes
MEDIA_BASE_URL=https://caja.segrd.com/api/img
```

## Logs

```
/var/log/lacajita/
├── image_optimization.log     # Logs de optimización
├── media_sync_*.log           # Logs de sincronización
├── integrity_report_*.json    # Reportes de validación
└── cron.log                   # Logs de cron
```

## Checklist de Media

- [ ] Imágenes optimizadas antes de deploy
- [ ] Thumbnails generados para nuevas imágenes
- [ ] Checksums actualizados
- [ ] Backup/versión creada
- [ ] URLs correctas en base de datos

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeturing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
