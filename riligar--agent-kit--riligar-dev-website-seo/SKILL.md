---
name: riligar-dev-website-seo
description: Implementação de infraestrutura de SEO técnico seguindo a stack RiLiGar (React, Vite, Bun, Elysia). Use para configurar sitemaps, robots.txt, meta tags, OpenGraph, dados estruturados (JSON-LD) e URLs canônicas. Use when this capability is needed.
metadata:
  author: riligar
---

# SEO Técnico (RiLiGar Tech Stack)

## Arquivos da Skill

Esta skill utiliza os seguintes arquivos de referência:

- **SKILL.md** (este arquivo): Guia central de implementação
- **references/implementation.md**: Templates de código para React/Vite e Bun/Elysia
- **references/checklist.md**: Checklist pré-lançamento
- **references/structured-data.md**: Templates de markup JSON-LD

## O que esta Skill cobre

1. **Sitemaps** → Geração dinâmica via Bun/Elysia ou estática via Vite/`public`.
2. **Robots.txt** → Diretivas para crawlers.
3. **Meta Tags** → OpenGraph, Twitter Cards, descrições e títulos.
4. **Dados Estruturados** → Schema markup (JSON-LD) para rich snippets.
5. **URLs Canônicas** → Prevenção de conteúdo duplicado.
6. **Performance & Vitals** → Otimização para os Core Web Vitals.

---

# Part 1: Implementação de Sitemap

Em projetos RiLiGar, o sitemap pode ser estático (em `public/sitemap.xml`) ou dinâmico via backend Elysia.

## Sitemap Estático (Vite `public/sitemap.xml`)

Para sites institucionais simples:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://riligar.com/</loc>
    <lastmod>2025-01-20</lastmod>
    <changefreq>weekly</changefreq>
    <priority>1.0</priority>
  </url>
</urlset>
```

## Sitemap Dinâmico (Elysia /backend)

Se o site tiver conteúdo dinâmico (postagens de blog, produtos), crie um endpoint no Elysia:

```javascript
// src/routes/seo.js
import { Elysia } from 'elysia'
import { db } from '../database/db'

export const seoRoutes = new Elysia().get('/sitemap.xml', async ({ set }) => {
    const posts = await db.query.posts.findMany()
    const baseUrl = 'https://riligar.com'

    const xml = `<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    <url>
        <loc>${baseUrl}</loc>
        <priority>1.0</priority>
    </url>
    ${posts
        .map(
            post => `
    <url>
        <loc>${baseUrl}/blog/${post.slug}</loc>
        <lastmod>${post.updatedAt.toISOString().split('T')[0]}</lastmod>
        <priority>0.7</priority>
    </url>`
        )
        .join('')}
</urlset>`

    set.headers['Content-Type'] = 'application/xml'
    return xml
})
```

---

# Part 2: Implementação de Robots.txt

O `robots.txt` deve ficar na pasta `public/` do frontend ou ser servido pelo backend.

```text
User-agent: *
Allow: /
Disallow: /api/
Disallow: /dashboard/
Disallow: /admin/

# Block AI training bots
User-agent: GPTBot
Disallow: /
User-agent: ChatGPT-User
Disallow: /
User-agent: CCBot
Disallow: /

Sitemap: https://riligar.com/sitemap.xml
```

---

# Part 3: Metadata e Meta Tags

Em SPAs (Single Page Applications) React/Vite, use metadados estáticos no `index.html` ou gerencie dinamicamente.

## No index.html (Baseline)

```html
<title>RiLiGar — Sua Tagline</title>
<meta
    name="description"
    content="Descrição chamativa de 150-160 caracteres."
/>

<!-- OpenGraph -->
<meta
    property="og:type"
    content="website"
/>
<meta
    property="og:title"
    content="RiLiGar — Sua Tagline"
/>
<meta
    property="og:description"
    content="Descrição para redes sociais."
/>
<meta
    property="og:image"
    content="https://riligar.com/og-image.png"
/>

<!-- Twitter -->
<meta
    name="twitter:card"
    content="summary_large_image"
/>
<meta
    name="twitter:title"
    content="RiLiGar — Sua Tagline"
/>
<meta
    name="twitter:image"
    content="https://riligar.com/og-image.png"
/>
```

## Dinâmico (Server-side Injection no Elysia)

Para melhores resultados de SEO em conteúdo dinâmico, o Elysia pode injetar as tags no HTML enviado:

```javascript
// Exemplo simples de injeção no Elysia
app.get('/*', async ({ path }) => {
    let html = await Bun.file('dist/index.html').text()

    if (path.startsWith('/blog/')) {
        const post = await getPost(path.split('/')[2])
        html = html.replace('<title>RiLiGar</title>', `<title>${post.title} | RiLiGar</title>`)
        html = html.replace('content="RiLiGar description"', `content="${post.excerpt}"`)
    }

    return new Response(html, { headers: { 'Content-Type': 'text/html' } })
})
```

---

# Part 4: Variáveis de Ambiente

Sempre configure a URL base nos arquivos de ambiente:

```bash
# .env.development
VITE_SITE_URL=http://localhost:5173
SITE_URL=http://localhost:3000

# .env.production
VITE_SITE_URL=https://riligar.com
SITE_URL=https://riligar.com
```

---

# Checklist de Implementação

1. [ ] `robots.txt` configurado e bloqueando `/admin` e `/dashboard`.
2. [ ] `sitemap.xml` gerado e acessível.
3. [ ] Meta tags OpenGraph e Twitter presentes em todas as páginas públicas.
4. [ ] Imagem OG de 1200x630px em `public/og-image.png`.
5. [ ] Canonical URLs apontando para a versão preferencial.
6. [ ] Dados estruturados (JSON-LD) para Organização no Home.
7. [ ] Headers de performance (preload de fontes) no index.html.

Para detalhes específicos, consulte os arquivos na pasta `references/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/riligar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
