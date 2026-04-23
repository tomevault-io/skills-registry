---
name: otto-frontend
description: Frontend specialist for Otto dashboard development. Use when working on React components, pages, hooks, or any client-side code in client/src/. Use when this capability is needed.
metadata:
  author: canivel
---

# Otto Frontend Specialist

Expert context for developing the Otto Agent dashboard (React + Vite).

## Tech Stack

- **React 19** with TypeScript 5.9
- **Vite 7** for bundling
- **Tailwind CSS 4** for styling
- **shadcn/ui** component library
- **React Router v7** for navigation
- **Clerk** for authentication
- **XTerm.js** for terminal emulation
- **Monaco Editor** for code editing

## Architecture Overview

```
client/src/
├── pages/           # Route components (16 pages)
├── components/      # Reusable UI components
│   └── ui/          # shadcn/ui base components
├── hooks/           # Custom React hooks
├── contexts/        # React Context providers
├── layouts/         # Page layout wrappers
├── lib/             # Utilities (api client, utils)
├── providers/       # App-level providers
└── types/           # TypeScript interfaces
```

## Component Patterns

### Page Component
Location: `client/src/pages/`

```tsx
import { useParams } from 'react-router-dom';
import { useOrganization } from '../hooks/useOrganization';
import { PageHeader } from '../components/PageHeader';

export function ProjectPage() {
  const { projectId } = useParams<{ projectId: string }>();
  const { organization } = useOrganization();

  const { data: project, isLoading } = useQuery({
    queryKey: ['project', projectId],
    queryFn: () => api.getProject(projectId!),
    enabled: !!projectId,
  });

  if (isLoading) return <LoadingSpinner />;
  if (!project) return <NotFound />;

  return (
    <div className="container mx-auto py-6">
      <PageHeader title={project.name} />
      {/* Page content */}
    </div>
  );
}
```

### Reusable Component
Location: `client/src/components/`

```tsx
import { cn } from '@/lib/utils';

interface StatusBadgeProps {
  status: 'pending' | 'running' | 'completed' | 'failed';
  className?: string;
}

export function StatusBadge({ status, className }: StatusBadgeProps) {
  const variants = {
    pending: 'bg-yellow-100 text-yellow-800',
    running: 'bg-blue-100 text-blue-800',
    completed: 'bg-green-100 text-green-800',
    failed: 'bg-red-100 text-red-800',
  };

  return (
    <span className={cn(
      'inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium',
      variants[status],
      className
    )}>
      {status}
    </span>
  );
}
```

## API Client Pattern

Location: `client/src/lib/api.ts`

```tsx
import { useAuth } from '@clerk/clerk-react';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3005';

export function useApi() {
  const { getToken } = useAuth();
  const organizationId = useOrganizationId();

  const fetchWithAuth = async (path: string, options: RequestInit = {}) => {
    const token = await getToken();

    const response = await fetch(`${API_URL}${path}`, {
      ...options,
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${token}`,
        'X-Organization-Id': organizationId,
        ...options.headers,
      },
    });

    if (!response.ok) {
      throw new Error(`API error: ${response.status}`);
    }

    return response.json();
  };

  return {
    get: (path: string) => fetchWithAuth(path),
    post: (path: string, data: unknown) =>
      fetchWithAuth(path, { method: 'POST', body: JSON.stringify(data) }),
    put: (path: string, data: unknown) =>
      fetchWithAuth(path, { method: 'PUT', body: JSON.stringify(data) }),
    delete: (path: string) =>
      fetchWithAuth(path, { method: 'DELETE' }),
  };
}
```

## Hook Patterns

Location: `client/src/hooks/`

```tsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { useApi } from '@/lib/api';

export function useProjects() {
  const api = useApi();

  return useQuery({
    queryKey: ['projects'],
    queryFn: () => api.get('/api/projects'),
  });
}

export function useCreateProject() {
  const api = useApi();
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (data: CreateProjectInput) =>
      api.post('/api/projects', data),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['projects'] });
    },
  });
}
```

## WebSocket Pattern

```tsx
import { useEffect, useRef } from 'react';
import { useAuth } from '@clerk/clerk-react';

export function useRunLogs(runId: string, onLog: (log: string) => void) {
  const { getToken } = useAuth();
  const wsRef = useRef<WebSocket | null>(null);

  useEffect(() => {
    let ws: WebSocket;

    const connect = async () => {
      const token = await getToken();
      ws = new WebSocket(`ws://localhost:3005/ws?token=${token}`);
      wsRef.current = ws;

      ws.onopen = () => {
        ws.send(JSON.stringify({
          type: 'subscribeRun',
          data: { runId },
        }));
      };

      ws.onmessage = (event) => {
        const msg = JSON.parse(event.data);
        if (msg.type === 'run.log') {
          onLog(msg.data.content);
        }
      };
    };

    connect();

    return () => {
      ws?.close();
    };
  }, [runId, getToken, onLog]);

  return wsRef;
}
```

## Terminal Component (XTerm.js)

```tsx
import { useEffect, useRef } from 'react';
import { Terminal } from '@xterm/xterm';
import { WebglAddon } from '@xterm/addon-webgl';
import { FitAddon } from '@xterm/addon-fit';
import '@xterm/xterm/css/xterm.css';

interface TerminalViewProps {
  logs: string[];
}

export function TerminalView({ logs }: TerminalViewProps) {
  const terminalRef = useRef<HTMLDivElement>(null);
  const xtermRef = useRef<Terminal | null>(null);

  useEffect(() => {
    if (!terminalRef.current) return;

    const term = new Terminal({
      theme: { background: '#1a1a2e' },
      fontSize: 14,
      fontFamily: 'JetBrains Mono, monospace',
    });

    const fitAddon = new FitAddon();
    term.loadAddon(fitAddon);
    term.loadAddon(new WebglAddon());

    term.open(terminalRef.current);
    fitAddon.fit();
    xtermRef.current = term;

    return () => term.dispose();
  }, []);

  useEffect(() => {
    logs.forEach(log => xtermRef.current?.writeln(log));
  }, [logs]);

  return <div ref={terminalRef} className="h-full w-full" />;
}
```

## shadcn/ui Usage

Components are in `client/src/components/ui/`. Import and use directly:

```tsx
import { Button } from '@/components/ui/button';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';
import { Dialog, DialogTrigger, DialogContent } from '@/components/ui/dialog';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Tabs, TabsList, TabsTrigger, TabsContent } from '@/components/ui/tabs';
```

Add new components: `npx shadcn-ui@latest add <component>`

## Tailwind CSS Patterns

```tsx
// Conditional classes with cn()
import { cn } from '@/lib/utils';

<div className={cn(
  'p-4 rounded-lg border',
  isActive && 'border-blue-500 bg-blue-50',
  isError && 'border-red-500 bg-red-50'
)} />

// Common patterns
<div className="container mx-auto py-6" />           // Page container
<div className="flex items-center gap-4" />          // Horizontal flex
<div className="grid grid-cols-3 gap-4" />           // Grid layout
<div className="space-y-4" />                        // Vertical spacing
```

## Routing

Location: `client/src/App.tsx` or router config

```tsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import { MainLayout } from './layouts/MainLayout';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route element={<MainLayout />}>
          <Route path="/" element={<DashboardPage />} />
          <Route path="/projects" element={<ProjectsPage />} />
          <Route path="/projects/:projectId" element={<ProjectPage />} />
          <Route path="/settings" element={<SettingsPage />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

## Key Pages Reference

| Page | Path | Purpose |
|------|------|---------|
| `DashboardPage` | `/` | Org overview, stats, activity |
| `ProjectsPage` | `/projects` | Project list |
| `ProjectPage` | `/projects/:id` | Project details, runs, stories |
| `StoriesPage` | `/projects/:id/stories` | Kanban board |
| `RunPage` | `/projects/:id/runs/:runId` | Run logs, terminal |
| `AgentsPage` | `/agents` | Agent pool management |
| `SettingsPage` | `/settings` | Org settings, Claude auth |
| `BillingPage` | `/billing` | Usage, costs |

## Key Components Reference

| Component | Purpose |
|-----------|---------|
| `Kanban` | Drag-and-drop story board |
| `TerminalView` | XTerm.js log viewer |
| `PRDEditor` | Monaco-based PRD editing |
| `RunCard` | Run status display |
| `StoryCard` | Story with status, assignee |
| `AgentCard` | Agent status, workload |

## Context Providers

```tsx
// Organization context
import { useOrganization } from '@/contexts/OrganizationContext';
const { organization, setOrganization } = useOrganization();

// Theme context (if exists)
import { useTheme } from '@/contexts/ThemeContext';
const { theme, setTheme } = useTheme();
```

## Environment Variables

Access in components via `import.meta.env`:

```tsx
const apiUrl = import.meta.env.VITE_API_URL;
const clerkKey = import.meta.env.VITE_CLERK_PUBLISHABLE_KEY;
```

Define in `client/.env`:
```
VITE_API_URL=http://localhost:3005
VITE_CLERK_PUBLISHABLE_KEY=pk_test_xxx
```

## Path Aliases

`@` maps to `client/src/`:

```tsx
import { Button } from '@/components/ui/button';
import { useProjects } from '@/hooks/useProjects';
import { cn } from '@/lib/utils';
```

## Commands

```bash
npm run client:dev      # Start dev server (port 5173)
npm run client:build    # Production build
npm run client:preview  # Preview production build
```

## Vite Proxy

API calls to `/api/*` and `/ws/*` are proxied to `localhost:3005` in development. No CORS issues.

## Testing

```bash
npm run test:dashboard  # Run dashboard tests
```

Test files: `*.test.tsx` next to components.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
