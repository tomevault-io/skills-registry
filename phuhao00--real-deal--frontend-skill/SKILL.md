---
name: frontend-skill
description: Frontend development for real_deal platform using Next.js 14.2, React 18.2, TypeScript 5.3.3, and Tailwind CSS 3.4.3. Use when working on web UI development, creating React components, building pages, integrating with backend APIs, or styling with custom CSS variables. Use when this capability is needed.
metadata:
  author: phuhao00
---

# Frontend Development (Next.js)

## Tech Stack

- **Framework**: Next.js 14.2.4 (App Router)
- **UI Library**: React 18.2.0
- **Language**: TypeScript 5.3.3
- **Styling**: Tailwind CSS 3.4.3 + CSS Variables
- **API Client**: Custom fetch wrapper

## Project Structure

```
web/
  app/
    page.tsx               # Home page (explore feed)
    globals.css            # Global styles and theme tokens
    layout.tsx             # Root layout
    bookmarks/             # Bookmarks page
    chat/                  # Chat interface
    companies/             # Company pages
    investors/             # Investor pages
    jobs/                  # Job listings
    login/                 # Login page
    media/                 # Media pages
    notifications/         # Notifications
    post/                  # Post creation
    profile/               # User profiles
    projects/              # Project pages
    products/              # Product pages
    lib/
      api.ts               # API client utility
  components/
    Card.tsx               # Simple card component
    Composer.tsx           # Post composer
    FacetFilter.tsx        # Search and filter
    FeedLayout.tsx         # Feed layout wrapper
    FeedRow.tsx             # Feed row item
    Navbar.tsx              # Navigation
    SidebarNav.tsx          # Sidebar navigation
    CenterHeader.tsx        # Header section
    RightSidebar.tsx        # Right sidebar
    # ... more components
  package.json
  tsconfig.json
  tailwind.config.js
  next.config.js
  postcss.config.js
```

## Key Patterns

### API Client Pattern

```typescript
// app/lib/api.ts
export const API_BASE = process.env.NEXT_PUBLIC_API_BASE || 'http://localhost:8080'

export async function api<T>(path: string, init?: RequestInit): Promise<T> {
  const res = await fetch(`${API_BASE}${path}`, {
    cache: 'no-store',
    credentials: 'include',
    ...init
  })
  if (!res.ok) throw new Error(`api ${path} ${res.status}`)
  return res.json()
}
```

### Server Component Pattern (App Router)

```typescript
// app/page.tsx - Server component
import { api } from './lib/api'
import FeedLayout from '../components/FeedLayout'
import Composer from '../components/Composer'
import FeedRow from '../components/FeedRow'

export default async function Page({ searchParams }: { searchParams?: { q?: string, type?: string, density?: string } }) {
  const data = await api<{projects:any[];products:any[];posts:any[];jobs:any[];companies:any[]}>('/api/explore')

  const feed = [
    ...data.projects.map(p=>({ id:p.id, title:p.title, subtitle:p.summary, tags:p.tags })),
    ...data.products.map(p=>({ id:p.id, title:p.name, subtitle:p.summary, tags:p.tags })),
    ...data.jobs.map(j=>({ id:j.id, title:j.title, subtitle:`${j.location} · ${j.level} · ${j.salary}`, tags:j.skills }))
  ]

  return (
    <FeedLayout trending={data.products} news={data.companies} title="动态">
      <Composer />
      <div className="divide-y divide-[color:var(--border)]">
        {feed.map((f:any)=> (
          <FeedRow key={f.id} title={f.title} subtitle={f.subtitle} tags={f.tags} />
        ))}
      </div>
    </FeedLayout>
  )
}
```

### Component Patterns

#### Simple Card Component
```typescript
// components/Card.tsx
export default function Card({ title, subtitle }: { title: string, subtitle?: string }){
  return (
    <div className="border border-neutral-800 p-4 rounded">
      <div className="font-medium">{title}</div>
      {subtitle ? <div className="text-sm text-neutral-400">{subtitle}</div> : null}
    </div>
  )
}
```

#### Feed Row Component
```typescript
// components/FeedRow.tsx
export default function FeedRow({ title, subtitle, tags, dense }: { title: string, subtitle?: string, tags?: string[], dense?: boolean }){
  return (
    <div className={`px-4 ${dense?'py-2':'py-3'} hover:bg-[color:color-mix(in oklab, var(--accent) 6%, transparent)] transition`}>
      <div className={`font-medium ${dense?'text-[15px]':'text-[16px]'}`}>{title}</div>
      {subtitle ? <div className={`${dense?'text-[14px]':'text-[15px]'} text-neutral-600 mt-0.5`}>{subtitle}</div> : null}
      {tags && tags.length ? <div className="text-[12px] mt-1 tag">{tags.join(', ')}</div> : null}
      <div className="mt-2 flex items-center gap-8 interactive-actions">
        <button className="flex items-center gap-1 interactive-action" aria-label="评论" type="button">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="1.6" strokeLinecap="round" strokeLinejoin="round">
            <path d="M21 15a4 4 0 0 1-4 4H7l-4 4V7a4 4 0 0 1 4-4h10a4 4 0 0 1 4 4v8z"/>
          </svg>
        </button>
      </div>
    </div>
  )
}
```

#### Composer Component
```typescript
// components/Composer.tsx
export default function Composer(){
  return (
    <div className="panel border rounded-2xl px-4 py-3 mb-4">
      <div className="text-sm text-neutral-600">有什么新鲜事？</div>
      <div className="mt-2 flex items-center gap-2">
        <input className="flex-1 bg-transparent outline-none text-sm" placeholder="分享你的动态、作品或招聘信息" />
        <a href="/post" className="btn-primary px-3 py-1 rounded-full text-sm">发布</a>
      </div>
    </div>
  )
}
```

#### Facet Filter Component
```typescript
// components/FacetFilter.tsx - Client component with hooks
"use client"
import { useRouter, useSearchParams, usePathname } from "next/navigation"

type Facet = { key: string; label: string; options: string[] }

export default function FacetFilter({ facets }: { facets: Facet[] }){
  const router = useRouter()
  const pathname = usePathname()
  const params = useSearchParams()

  const setParam = (key: string, values: string[]) => {
    const usp = new URLSearchParams(params.toString())
    if (values.length) usp.set(key, values.join(',')); else usp.delete(key)
    router.push(`${pathname}?${usp.toString()}`)
  }

  const toggle = (key: string, value: string) => {
    const cur = (params.get(key) || '').split(',').filter(Boolean)
    const next = cur.includes(value) ? cur.filter(v=>v!==value) : [...cur, value]
    setParam(key, next)
  }

  // ... rest of component
}
```

### Theme & Styling

#### CSS Variables (in globals.css)
```css
:root {
  --bg: 255 255 255;
  --fg: 0 0 0;
  --border: 240 240 240;
  --accent: 0 100 220;
  /* ... more tokens */
}
```

#### Tailwind Custom Classes
```css
.globals.css:
.btn-primary {
  @apply bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700;
}
.panel {
  @apply bg-white border rounded-lg p-4;
}
.chip {
  @apply px-3 py-1 rounded-full text-sm border;
}
```

## Available Backend APIs

```typescript
// Explore & Content
GET /api/explore         // Get all content (projects, products, posts, jobs, companies)
GET /api/projects        // List projects
GET /api/products        // List products
GET /api/posts           // List posts
GET /api/jobs            // List jobs
GET /api/companies/:id   // Get company details

// Auth & User
POST /api/login          // Login
GET /api/me              // Get current user
GET /api/users/:id       // Get user profile

// VC/YC Features
GET /api/investors       // List investors
GET /api/pitch/:id       // Get pitch page
GET /api/deal-room/:id   // Get deal room

// Media & Assets
GET /api/media/:id       // Get media
GET /api/media-assets    // List media assets

// Compliance & Verification
GET /api/company-verifications/:companyId
GET /api/job-compliance/:jobId
GET /api/content-moderation/:id

// Billing & Quota
GET /api/usage           // Get usage stats
GET /api/quota           // Get quota limits
GET /api/capacity-packs  // List capacity packs
GET /api/job-slots       // Get job slots
GET /api/charges         // List charges

// Notifications
GET /api/inbox           // Get inbox
GET /api/notification-preferences  // Get preferences
```

## Development Commands

```bash
cd web

# Development
npm run dev              # Start dev server on :3000

# Production
npm run build            # Build for production
npm start                # Start production server

# Type checking
npx tsc --noEmit         # TypeScript type check

# Linting (if configured)
npm run lint
```

## Common Tasks

### Create New Page
```bash
# Create page in app directory
touch web/app/your-page/page.tsx

# Example:
export default async function YourPage() {
  const data = await api<DataType>('/api/endpoint')
  return <div>{/* content */}</div>
}
```

### Create New Component
```typescript
// Create in components/
// web/components/YourComponent.tsx
export default function YourComponent({ prop1, prop2 }: Props) {
  return (
    <div className="...">
      {/* component content */}
    </div>
  )
}
```

### Add Interactive State (Client Component)
```typescript
"use client"

import { useState } from 'react'

export default function InteractiveComponent() {
  const [count, setCount] = useState(0)

  return (
    <button onClick={() => setCount(c => c + 1)}>
      Count: {count}
    </button>
  )
}
```

### Handle Authentication
```typescript
// Login page example (client component)
"use client"

export default function LoginPage() {
  const handleLogin = async () => {
    await fetch('http://localhost:8080/api/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      credentials: 'include',
      body: JSON.stringify({ email: 'user@example.com' })
    })
    window.location.href = '/'
  }

  return <button onClick={handleLogin}>Login</button>
}
```

## Routing

```typescript
// App Router file-based routing
app/page.tsx              // /
app/login/page.tsx        // /login
app/jobs/page.tsx         // /jobs
app/companies/[id]/page.tsx  // /companies/123

// Dynamic routes with params
export default async function CompanyPage({ params }: { params: { id: string } }) {
  const company = await api<Company>(`/api/companies/${params.id}`)
  return <div>{company.name}</div>
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuhao00) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
