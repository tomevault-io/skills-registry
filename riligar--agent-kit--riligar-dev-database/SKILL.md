---
name: riligar-dev-database
description: database patterns for RiLiGar using Drizzle ORM + bun:sqlite. Use when setting up database connections, defining schemas, creating migrations, or writing queries. Covers SQLite on Fly.io volumes with the drizzle-kit workflow. Use when this capability is needed.
metadata:
  author: riligar
---

# database — Drizzle + bun:sqlite

> SQLite nativo no Bun. Zero drivers externos. Base de dados no volume do Fly.io (`/app/data`).

## Referências

| Arquivo                                   | Quando usar                                          |
| ----------------------------------------- | ---------------------------------------------------- |
| [connection.md](references/connection.md) | Setup inicial: instalação, db.js, drizzle.config     |
| [schema.md](references/schema.md)         | Definir tabelas, tipos de colunas, relações          |
| [migrations.md](references/migrations.md) | Criar e executar migrations com drizzle-kit          |
| [queries.md](references/queries.md)       | Select, insert, update, delete, queries com relações |

## Quick Start

```javascript
// database/db.js
import { drizzle } from 'drizzle-orm/bun-sqlite'
import database from 'bun:sqlite'

const sqlite = new database(process.env.DB_PATH ?? './database/database.db')
const db = drizzle({ client: sqlite })

export { db }
```

## Regras

- **Caminho do banco:** `/app/data/database.db` em produção (volume Fly.io). `./database/database.db` em desenvolvimento.
- **Migrations sempre:** Use `drizzle-kit generate` + `drizzle-kit migrate`. Nunca edite migrations à mão.
- **Schema único:** Todas as tabelas em `database/schema.js`.
- **Migrations no startup:** Use `migrate()` no `index.js` antes de `.listen()`.

## Related Skills

| Need                  | Skill                                 |
| --------------------- | ------------------------------------- |
| **Backend (Elysia)**  | @[.agent/skills/riligar-dev-manager]  |
| **Payments (Stripe)** | @[.agent/skills/riligar-infra-stripe] |
| **Infrastructure**    | @[.agent/skills/riligar-infra-fly]    |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/riligar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
