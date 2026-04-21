---
name: moodle-admin
description: Moodle LMS administration via REST API. Manage courses, users, enrollments, grades, and content. Use when user asks to manage Moodle courses, enroll students, check grades, create assignments, or administer Moodle LMS. Use when this capability is needed.
metadata:
  author: xukrutdonut
---

# Moodle Admin Skill

Administración completa de Moodle LMS via Web Services API.

## Setup

Requiere configuración de Web Services en Moodle:

1. **Activar Web Services:**
   - Site administration → Advanced features → Enable web services

2. **Crear Token:**
   - Site administration → Server → Web services → Manage tokens
   - Create token for user

3. **Configurar Variables:**
```bash
export MOODLE_URL="https://tu-moodle.com"
export MOODLE_TOKEN="tu_token_aqui"
```

## Funcionalidades

### Gestión de Cursos
- Listar cursos
- Crear/modificar cursos
- Obtener contenidos
- Gestionar secciones

### Gestión de Usuarios
- Listar usuarios
- Crear/modificar usuarios
- Buscar por email/nombre
- Gestionar perfiles

### Matrículas (Enrollments)
- Matricular usuarios en cursos
- Des-matricular
- Cambiar roles
- Listar matriculados

### Calificaciones
- Obtener calificaciones de curso
- Actualizar notas
- Exportar calificaciones
- Análisis estadístico de rendimiento

### Contenidos
- Subir recursos
- Crear actividades
- Gestionar tareas
- Programar eventos

## Uso

```bash
python3 scripts/moodle_admin.py --list-courses
python3 scripts/moodle_admin.py --course-id 123 --list-students
python3 scripts/moodle_admin.py --enroll --user-id 456 --course-id 123 --role student
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xukrutdonut) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
