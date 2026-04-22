---
name: nextjs-app-router
description: Master Next.js 16 App Router development with route groups, server components, security-first patterns, and production-grade implementations. Use when building admin portals, public sites, API routes, or implementing secure routing in Next.js applications. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# Next.js App Router Mastery

A comprehensive guide to building production-grade applications with Next.js 16 App Router, featuring security-first architecture, route groups for surface separation, and enterprise-level patterns from the Vantus Systems codebase.

## Quick Start

Create a secure Next.js App Router application with surface separation:

```typescript
// app/layout.tsx - Root layout with security providers
export const dynamic = "force-dynamic";

export default async function RootLayout({ children }) {
  const nonce = crypto.randomUUID().replace(/-/g, "");
  const session = await getServerSession(authOptions);

  return (
    <html lang="en" suppressHydrationWarning>
      <body>
        <AuthProvider session={session}>
          <ThemeProvider nonce={nonce}>
            {children}
          </ThemeProvider>
        </AuthProvider>
      </body>
    </html>
  );
}
```

## Core Concepts

### Route Groups for Surface Separation

Organize routes by surface boundaries to maintain clean architecture:

```typescript
// app/(site)/layout.tsx - Public site layout
export default function SiteLayout({ children }) {
  return (
    <>
      <SystemLayer />
      <ConsoleHud />
      <div className="relative z-10 min-h-dvh flex flex-col">
        <Header />
        <PageTransition>
          <ErrorBoundary>
            <main className="flex-1 pt-20">{children}</main>
          </ErrorBoundary>
        </PageTransition>
        <Footer />
      </div>
    </>
  );
}

// app/(admin)/admin/(dashboard)/layout.tsx - Admin dashboard layout
export default async function AdminLayout({ children }) {
  const session = await getServerSession(authOptions);
  if (!session?.user?.email) redirect("/admin/login");

  return (
    <div className="flex min-h-screen w-full flex-col bg-muted/40">
      <AdminSidebar />
      <div className="flex flex-1 flex-col overflow-hidden">
        <AdminHeader />
        <main className="flex-1 overflow-auto p-4 md:p-6">
          {children}
        </main>
      </div>
    </div>
  );
}
```

### Proxy-Based Request Interception

Use `proxy.ts` as the centralized security boundary instead of middleware:

```typescript
// proxy.ts - Centralized request interception
export async function proxy(request: NextRequest) {
  const nonce = crypto.randomUUID().replace(/-/g, "");
  requestHeaders.set("x-nonce", nonce);

  // CSP with nonce enforcement
  const csp = [
    "default-src 'self'",
    `script-src 'self' 'nonce-${nonce}' 'unsafe-eval'`,
    "style-src 'self' 'unsafe-inline'",
    "connect-src 'self'",
    "object-src 'none'",
    "base-uri 'self'",
    "form-action 'self'",
    "frame-ancestors 'none'",
    "upgrade-insecure-requests",
  ].join(" ; ");

  // Admin route protection with session validation
  if (isAdminRoute && !isLoginRoute) {
    const token = await getToken({ req: request, secret });
    if (!token) return NextResponse.redirect(new URL("/admin/login", request.url));

    const validationResult = await validateSession(token.sessionToken);
    if (!validationResult.valid) {
      return NextResponse.redirect(new URL("/admin/login", request.url));
    }
  }

  return NextResponse.next({ request: { headers: requestHeaders } });
}
```

## Workflows

### Creating Secure Admin Routes

1. **Define API route with security guards**
```typescript
// app/api/admin/users/route.ts
export const dynamic = "force-dynamic";

export async function GET() {
  try {
    const user = await requireAdmin({ permissions: ["users.read"] });

    const users = await prisma.user.findMany({
      where: { deletedAt: null, tenantId: user.tenantId },
      select: SAFE_USER_WITH_ROLES_SELECT,
    });

    return jsonNoStore(users);
  } catch {
    return jsonNoStore({ error: "Unauthorized" }, { status: 401 });
  }
}

export async function POST(req: NextRequest) {
  assertSameOrigin(req);
  const actor = await requireAdmin({ permissions: ["users.write"] });

  const validatedData = createUserSchema.parse(await req.json());
  // ... create user with audit logging
}
```

2. **Create admin page with server-side auth**
```typescript
// app/(admin)/admin/(dashboard)/users/page.tsx
export const dynamic = "force-dynamic";

export default async function UsersPage() {
  await requireAdmin({ permissions: ["users.read"] });

  const users = await prisma.user.findMany({
    where: { deletedAt: null },
    select: SAFE_USER_WITH_ROLES_SELECT,
  });

  return <UsersTable users={users} />;
}
```

### Implementing Route Groups

1. **Public site routes** (`app/(site)/*`)
   - Marketing pages, contact forms, public content
   - Client components for interactivity
   - No authentication required

2. **Admin routes** (`app/(admin)/admin/*`)
   - Dashboard, CRUD operations, settings
   - Server-side authentication checks
   - RBAC permission enforcement

3. **API routes** (`app/api/admin/*`)
   - Data operations, file uploads, webhooks
   - CSRF protection, audit logging
   - Tenant isolation

## Examples

### Complete Admin CRUD Implementation

**API Route** (`app/api/admin/projects/route.ts`):
```typescript
import { requireAdmin } from "@/lib/admin/guards";
import { createAuditLog } from "@/lib/admin/audit";
import { jsonNoStore } from "@/lib/security/response";
import { assertSameOrigin } from "@/lib/security/origin";

const createProjectSchema = z.object({
  name: z.string().min(1),
  description: z.string().optional(),
  status: z.enum(["draft", "active", "completed"]),
});

export async function GET() {
  const user = await requireAdmin({ permissions: ["projects.read"] });

  const projects = await prisma.project.findMany({
    where: {
      deletedAt: null,
      ...(user.tenantId && { tenantId: user.tenantId })
    },
    include: { client: true },
  });

  return jsonNoStore(projects);
}

export async function POST(req: NextRequest) {
  assertSameOrigin(req);
  const actor = await requireAdmin({ permissions: ["projects.write"] });

  const body = await req.json();
  const validatedData = createProjectSchema.parse(body);

  const project = await prisma.project.create({
    data: {
      ...validatedData,
      tenantId: actor.tenantId,
    },
  });

  await createAuditLog({
    action: "create_project",
    resource: "project",
    resourceId: project.id,
    actorId: actor.id,
    after: project,
  });

  return jsonNoStore(project, { status: 201 });
}
```

**Admin Page** (`app/(admin)/admin/(dashboard)/projects/page.tsx`):
```typescript
import { prisma } from "@/lib/prisma";
import { requireAdmin } from "@/lib/admin/guards";
import { ProjectsTable } from "@/components/admin/projects/projects-table";

export const dynamic = "force-dynamic";

export default async function ProjectsPage() {
  await requireAdmin({ permissions: ["projects.read"] });

  const projects = await prisma.project.findMany({
    where: { deletedAt: null },
    include: { client: true },
    orderBy: { createdAt: "desc" },
  });

  return (
    <div className="space-y-6">
      <div className="flex items-center justify-between">
        <h1 className="text-2xl font-bold tracking-tight">Projects</h1>
        <Button asChild>
          <Link href="/admin/projects/new">New Project</Link>
        </Button>
      </div>
      <ProjectsTable projects={projects} />
    </div>
  );
}
```

### Public Site with Client Components

**Page Component** (`app/(site)/page.tsx`):
```typescript
import { HeroBackground } from "@/components/hero-background";
import { BuildPlanModule } from "@/components/build-plan-module";
import { AuditModal } from "@/components/audit-modal";

export default function Home() {
  return (
    <div className="flex flex-col min-h-screen">
      <section className="relative min-h-[90vh] flex flex-col justify-center">
        <HeroBackground />
        <div className="container relative z-10 px-4 md:px-6 flex flex-col lg:flex-row items-center gap-12">
          <div className="flex-1 flex flex-col items-center lg:items-start">
            <CalibrationHeadline text={siteConfig.tagline} />
            <p className="text-xl text-muted-foreground max-w-xl mb-10">
              No vague estimates. No vendor lock-in. Just a clear, engineered path.
            </p>
            <div className="flex flex-col sm:flex-row gap-4">
              <AuditModal />
              <Button asChild variant="ghost">
                <Link href="/work">See real outcomes</Link>
              </Button>
            </div>
          </div>
          <div className="flex-1 w-full max-w-2xl">
            <BuildPlanModule />
          </div>
        </div>
      </section>
    </div>
  );
}
```

## Security Patterns

### CSRF Protection in API Routes

```typescript
// Always check same-origin for mutations
export async function POST(req: NextRequest) {
  assertSameOrigin(req); // Throws on CSRF attempts

  const actor = await requireAdmin({ permissions: ["resource.write"] });
  // ... proceed with mutation
}
```

### Safe Data Selection

```typescript
// lib/security/safe-user.ts
export const SAFE_USER_WITH_ROLES_SELECT = {
  id: true,
  name: true,
  email: true,
  createdAt: true,
  updatedAt: true,
  roles: {
    select: {
      role: {
        select: {
          id: true,
          name: true,
          permissions: true,
        },
      },
    },
  },
} as const;
```

### Tenant Isolation

```typescript
// Always scope queries by tenant
const user = await requireAdmin({ permissions: ["resource.read"] });

const resources = await prisma.resource.findMany({
  where: {
    deletedAt: null,
    ...(user.tenantId && { tenantId: user.tenantId }), // Tenant scoping
  },
});
```

## Advanced Features

### Dynamic Route Segments

```typescript
// app/(admin)/admin/(dashboard)/projects/[id]/page.tsx
interface PageProps {
  params: Promise<{ id: string }>;
}

export default async function ProjectDetailPage({ params }: PageProps) {
  const { id } = await params;
  await requireAdmin({ permissions: ["projects.read"] });

  const project = await prisma.project.findUnique({
    where: { id, deletedAt: null },
    include: { client: true, timeEntries: true },
  });

  if (!project) notFound();

  return <ProjectDetail project={project} />;
}
```

### Parallel Data Fetching

```typescript
// app/(admin)/admin/(dashboard)/page.tsx
export default async function AdminDashboard() {
  await requireAdmin({ permissions: ["admin.access"] });

  const [leadsCount, projectsCount, incidentsCount] = await Promise.all([
    prisma.lead.count({ where: { status: "new" } }),
    prisma.project.count({ where: { status: "active" } }),
    prisma.incident.count({ where: { status: "open" } }),
  ]);

  return <DashboardStats {...{ leadsCount, projectsCount, incidentsCount }} />;
}
```

## Error Handling

### Global Error Boundaries

```typescript
// app/(site)/layout.tsx
<PageTransition>
  <ErrorBoundary>
    <main className="flex-1 pt-20">{children}</main>
  </ErrorBoundary>
</PageTransition>
```

### API Error Responses

```typescript
export async function POST(req: NextRequest) {
  try {
    // ... validation and processing
  } catch (error) {
    if (error instanceof z.ZodError) {
      return jsonNoStore({ error: error.errors }, { status: 400 });
    }
    if (error instanceof Error && error.message.includes("Origin")) {
      return jsonNoStore({ error: error.message }, { status: 403 });
    }
    return jsonNoStore({ error: "Internal Server Error" }, { status: 500 });
  }
}
```

## Best Practices Summary

- **Route Groups**: Use `(site)`, `(admin)` for surface separation
- **Security First**: Proxy-based auth, CSRF protection, tenant isolation
- **Server Components**: Prefer for data fetching and auth checks
- **Client Components**: Only when interactivity is required
- **Audit Everything**: Log all privileged mutations
- **Safe Selects**: Never expose sensitive data accidentally
- **Force Dynamic**: Use for per-request nonce and fresh data

This Skill enables building enterprise-grade Next.js applications with the same security and architectural standards as Vantus Systems.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
