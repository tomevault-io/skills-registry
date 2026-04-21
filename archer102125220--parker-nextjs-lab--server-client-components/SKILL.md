---
name: server-vs-client-components
description: Decision tree for choosing between Server and Client Components in Next.js App Router Use when this capability is needed.
metadata:
  author: archer102125220
---

# Server vs Client Components

## 🎯 When to Use This Skill

Use this skill when:
- Creating new pages or components
- Deciding whether to add `'use client'`
- Refactoring Client Components to Server Components
- Optimizing bundle size and performance
- **Confused about when to use `'use client'`**
- Experiencing large bundle sizes
- Debugging hydration errors
- Designing component architecture

## 📋 Decision Tree

### Question 1: Does it need interactivity?

**Interactivity includes**:
- Event handlers (`onClick`, `onChange`, `onSubmit`, `onFocus`, etc.)
- React hooks (`useState`, `useEffect`, `useContext`, `useReducer`, etc.)
- Browser APIs (`window`, `localStorage`, `navigator`, `document`)
- Third-party libraries that depend on browser environment

- **YES** → Client Component (`'use client'`)
- **NO** → Continue to Question 2

### Question 2: Does it need to fetch data or access backend resources?

**Backend resources include**:
- Database queries
- File system access
- Environment variables (server-only)
- API keys and secrets

- **YES** → Server Component (default)
- **NO** → Continue to Question 3

### Question 3: Is it static content?

**Static content includes**:
- Text, images, icons
- Layout structure
- Non-interactive UI elements

- **YES** → Server Component (default)
- **NO** → Re-evaluate Question 1

### Rule of Thumb

**Default to Server Components**. Only use Client Components when you need:
1. ✅ Interactivity (event handlers, hooks)
2. ✅ Browser APIs
3. ✅ Third-party libraries that depend on browser environment

**Benefits of Server Components**:
- ✅ Smaller bundle size (code stays on server)
- ✅ Direct database access
- ✅ Better SEO (fully rendered HTML)
- ✅ Faster initial page load

**When to use Client Components**:
- ✅ Interactive UI (forms, buttons, modals)
- ✅ State management (useState, useReducer)
- ✅ Effects and subscriptions (useEffect)
- ✅ Browser-only features (localStorage, geolocation)

---

## ✅ Correct Examples

### Example 1: Server Component (Data Fetching)

```typescript
// ✅ CORRECT - Server Component (no 'use client')
// app/[locale]/users/page.tsx
import { getUsers } from '@/lib/api';
import { UserList } from '@/components/UserList';

export default async function UsersPage() {
  const users = await getUsers();  // ✅ Direct data fetching on server
  
  return (
    <div>
      <h1>Users</h1>
      <UserList users={users} />  {/* Pass data to Client Component */}
    </div>
  );
}
```

**Benefits**:
- ✅ No client-side data fetching code in bundle
- ✅ Direct database access (if needed)
- ✅ SEO-friendly (fully rendered HTML)
- ✅ No loading states needed

---

### Example 2: Client Component (Interactivity)

```typescript
// ✅ CORRECT - Client Component
// components/UserList/index.tsx
'use client';

import { useState } from 'react';
import type { User } from '@/types';

interface Props {
  users: User[];
}

export function UserList({ users }: Props) {
  const [filter, setFilter] = useState('');  // ✅ Needs useState
  const [sortBy, setSortBy] = useState<'name' | 'email'>('name');
  
  const filteredUsers = users.filter(user =>
    user.name.toLowerCase().includes(filter.toLowerCase())
  );
  
  const sortedUsers = [...filteredUsers].sort((a, b) =>
    a[sortBy].localeCompare(b[sortBy])
  );
  
  return (
    <div>
      <input 
        type="text"
        value={filter} 
        onChange={(e) => setFilter(e.target.value)}  // ✅ Needs event handler
        placeholder="Filter users..."
      />
      <select value={sortBy} onChange={(e) => setSortBy(e.target.value as 'name' | 'email')}>
        <option value="name">Sort by Name</option>
        <option value="email">Sort by Email</option>
      </select>
      <ul>
        {sortedUsers.map(user => (
          <li key={user.id}>{user.name} - {user.email}</li>
        ))}
      </ul>
    </div>
  );
}
```

**Why Client Component**:
- ✅ Uses `useState` for filter and sort
- ✅ Uses event handlers (`onChange`)
- ✅ Interactive filtering and sorting

---

### Example 3: Children Pattern (Server wraps Client wraps Server)

This is a powerful pattern that allows Server Components to be nested inside Client Components.

```typescript
// ✅ CORRECT - Server Component (Layout)
// app/[locale]/layout.tsx
import { ClientProvider } from '@/components/ClientProvider';
import { ServerSidebar } from '@/components/ServerSidebar';
import { ServerFooter } from '@/components/ServerFooter';

export default function Layout({ children }: Props) {
  return (
    <html>
      <body>
        <ClientProvider>  {/* Client Component for state management */}
          <ServerSidebar />  {/* ✅ Server Component as children */}
          <main>{children}</main>
          <ServerFooter />  {/* ✅ Server Component as children */}
        </ClientProvider>
      </body>
    </html>
  );
}
```

```typescript
// ✅ CORRECT - Client Component (Provider)
// components/ClientProvider/index.tsx
'use client';

import { ReactNode } from 'react';
import { ThemeProvider } from '@/contexts/ThemeContext';

interface Props {
  children: ReactNode;
}

export function ClientProvider({ children }: Props) {
  return (
    <ThemeProvider>
      {children}  {/* ✅ Can contain Server Components */}
    </ThemeProvider>
  );
}
```

```typescript
// ✅ CORRECT - Server Component (Sidebar)
// components/ServerSidebar/index.tsx
import { getNavigationItems } from '@/lib/api';

export async function ServerSidebar() {
  const items = await getNavigationItems();  // ✅ Server-side data fetching
  
  return (
    <aside>
      <nav>
        {items.map(item => (
          <a key={item.id} href={item.url}>{item.label}</a>
        ))}
      </nav>
    </aside>
  );
}
```

**Benefits of Children Pattern**:
- ✅ Client Component provides interactivity (theme, state)
- ✅ Server Components inside can fetch data
- ✅ Best of both worlds

---

### Example 4: Splitting Large Client Component

**Before** (entire page is client):
```typescript
// ❌ WRONG - Entire page is client-side
'use client';

import { useState } from 'react';

export default function ProductPage({ productId }: Props) {
  const [quantity, setQuantity] = useState(1);
  
  // ❌ Can't use async/await in Client Component
  const product = useProductQuery(productId);  // Client-side fetching
  
  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
      <input 
        type="number" 
        value={quantity} 
        onChange={(e) => setQuantity(Number(e.target.value))} 
      />
      <button>Add to Cart</button>
    </div>
  );
}
```

**After** (split into Server + Client):
```typescript
// ✅ CORRECT - Server Component (Page)
// app/[locale]/products/[id]/page.tsx
import { getProduct } from '@/lib/api';
import { ProductDetails } from '@/components/ProductDetails';
import { AddToCartForm } from '@/components/AddToCartForm';

export default async function ProductPage({ params }: Props) {
  const { id } = await params;
  const product = await getProduct(id);  // ✅ Server-side data fetching
  
  return (
    <div>
      <ProductDetails product={product} />  {/* Server Component */}
      <AddToCartForm productId={product.id} />  {/* Client Component */}
    </div>
  );
}
```

```typescript
// ✅ CORRECT - Server Component (Static Content)
// components/ProductDetails/index.tsx
export function ProductDetails({ product }: Props) {
  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
      <img src={product.image} alt={product.name} />
    </div>
  );
}
```

```typescript
// ✅ CORRECT - Client Component (Interactive Form)
// components/AddToCartForm/index.tsx
'use client';

import { useState } from 'react';

export function AddToCartForm({ productId }: Props) {
  const [quantity, setQuantity] = useState(1);
  
  const handleAddToCart = () => {
    // Add to cart logic
  };
  
  return (
    <div>
      <input 
        type="number" 
        value={quantity} 
        onChange={(e) => setQuantity(Number(e.target.value))} 
      />
      <button onClick={handleAddToCart}>Add to Cart</button>
    </div>
  );
}
```

**Benefits**:
- ✅ Reduced bundle size (ProductDetails stays on server)
- ✅ Server-side data fetching (faster, more secure)
- ✅ Only interactive parts are client-side

---

## ❌ Common Mistakes

### Mistake 1: Marking Entire Page as Client

```typescript
// ❌ WRONG - Entire page is client-side
'use client';

export default async function Page() {
  const data = await fetchData();  // ❌ Can't use async in Client Component
  return <div>{data}</div>;
}

// ✅ CORRECT - Split into Server + Client
// page.tsx (Server Component)
export default async function Page() {
  const data = await fetchData();  // ✅ Server-side data fetching
  return <ClientContent data={data} />;
}

// ClientContent.tsx (Client Component)
'use client';
export function ClientContent({ data }: Props) {
  const [state, setState] = useState();
  return <div>{data}</div>;
}
```

**Why wrong**: 
- ❌ Entire page code goes to client bundle
- ❌ Can't use async/await in Client Components
- ❌ Loses SEO benefits

---

### Mistake 2: Not Using Children Pattern

```typescript
// ❌ WRONG - Importing Server Component into Client Component
'use client';

import { ServerSidebar } from './ServerSidebar';  // ❌ Becomes Client Component

export function Layout({ children }: Props) {
  return (
    <div>
      <ServerSidebar />  {/* ❌ Now a Client Component */}
      {children}
    </div>
  );
}

// ✅ CORRECT - Use children pattern
// layout.tsx (Server Component)
import { ClientLayout } from './ClientLayout';
import { ServerSidebar } from './ServerSidebar';

export default function Layout({ children }: Props) {
  return (
    <ClientLayout sidebar={<ServerSidebar />}>  {/* ✅ Pass as prop */}
      {children}
    </ClientLayout>
  );
}

// ClientLayout.tsx (Client Component)
'use client';
export function ClientLayout({ sidebar, children }: Props) {
  return (
    <div>
      {sidebar}  {/* ✅ Stays as Server Component */}
      {children}
    </div>
  );
}
```

**Why wrong**: Importing a Server Component into a Client Component makes it a Client Component.

---

### Mistake 3: Using Client Component for Static Content

```typescript
// ❌ WRONG - Client Component for static content
'use client';

export function Header() {
  return (
    <header>
      <h1>My Website</h1>
      <nav>
        <a href="/">Home</a>
        <a href="/about">About</a>
      </nav>
    </header>
  );
}

// ✅ CORRECT - Server Component (no 'use client')
export function Header() {
  return (
    <header>
      <h1>My Website</h1>
      <nav>
        <a href="/">Home</a>
        <a href="/about">About</a>
      </nav>
    </header>
  );
}
```

**Why wrong**: No interactivity needed, adds unnecessary code to client bundle.

---

### Mistake 4: Fetching Data in Client Component

```typescript
// ❌ WRONG - Client-side data fetching
'use client';

import { useEffect, useState } from 'react';

export function UserList() {
  const [users, setUsers] = useState([]);
  
  useEffect(() => {
    fetch('/api/users')
      .then(res => res.json())
      .then(setUsers);
  }, []);
  
  return <div>{/* ... */}</div>;
}

// ✅ CORRECT - Server Component fetches, Client Component renders
// page.tsx (Server Component)
import { getUsers } from '@/lib/api';
import { UserListClient } from '@/components/UserListClient';

export default async function Page() {
  const users = await getUsers();  // ✅ Server-side
  return <UserListClient users={users} />;
}
```

**Why wrong**:
- ❌ Slower (network roundtrip from browser)
- ❌ Exposes API endpoints
- ❌ Loading states needed
- ❌ SEO issues (content not in initial HTML)

---

## 📝 Checklist

### Before Creating a Component

- [ ] Identified if component needs interactivity (event handlers, hooks)
- [ ] Checked if component uses React hooks
- [ ] Verified if component uses browser APIs
- [ ] Determined if component needs to fetch data
- [ ] Considered if component is static content

### When Refactoring

- [ ] Identified parts that need interactivity
- [ ] Split large Client Components into Server + Client
- [ ] Used children pattern for nested Server Components
- [ ] Moved data fetching to Server Components
- [ ] Verified bundle size reduction

### Performance Optimization

- [ ] Measured bundle size before and after
- [ ] Checked if Server Components can replace Client Components
- [ ] Used React DevTools to identify Client Components
- [ ] Verified no unnecessary `'use client'` directives

## 💡 Pro Tips

### Tip 1: Push `'use client'` to Leaf Components

Don't mark entire pages or layouts as client. Push `'use client'` as deep as possible.

```typescript
// ❌ BAD - Entire page is client
'use client';

export default function Page() {
  return (
    <div>
      <Header />  {/* ❌ Becomes client */}
      <Content />  {/* ❌ Becomes client */}
      <InteractiveButton />  {/* ✅ Needs to be client */}
    </div>
  );
}

// ✅ GOOD - Only interactive part is client
export default function Page() {
  return (
    <div>
      <Header />  {/* ✅ Server Component */}
      <Content />  {/* ✅ Server Component */}
      <InteractiveButton />  {/* ✅ Client Component */}
    </div>
  );
}
```

---

### Tip 2: Use Children Pattern for Composition

When you need a Client Component to wrap Server Components, use the children pattern:

```typescript
// ✅ GOOD - Children pattern
// layout.tsx (Server)
export default function Layout({ children }: Props) {
  return (
    <ClientProvider>
      <ServerSidebar />  {/* ✅ Stays Server Component */}
      {children}
    </ClientProvider>
  );
}

// ClientProvider.tsx (Client)
'use client';
export function ClientProvider({ children }: Props) {
  return <ThemeProvider>{children}</ThemeProvider>;
}
```

---

### Tip 3: Measure Bundle Size

Use Next.js bundle analyzer to see the impact:

```bash
# Install
yarn add -D @next/bundle-analyzer

# next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer(nextConfig);

# Run
ANALYZE=true yarn build
```

---

### Tip 4: Server Component Benefits

Remember the benefits of Server Components:

| Benefit | Description |
|---------|-------------|
| **Smaller bundle** | Code stays on server, not sent to client |
| **Direct DB access** | No API layer needed |
| **Better SEO** | Fully rendered HTML |
| **Faster load** | Less JavaScript to parse |
| **Secure** | API keys and secrets stay on server |

---

### Tip 5: When to Use Client Components

Only use Client Components when you need:

| Feature | Example |
|---------|---------|
| **Event handlers** | `onClick`, `onChange`, `onSubmit` |
| **React hooks** | `useState`, `useEffect`, `useContext` |
| **Browser APIs** | `window`, `localStorage`, `navigator` |
| **Third-party libs** | Libraries that depend on `window` |

---

## 🔗 Related Rules

- `.agent/rules/server-client-components.md`
- `GEMINI.md` - Server Components vs Client Components section
- `CLAUDE.md` - Server Components vs Client Components section

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archer102125220) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
