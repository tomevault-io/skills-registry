---
name: frontend-patterns
description: Padr\u00f5es de data fetching com TanStack Query, hooks customizados, estrutura de componentes, estado global e formul\u00e1rios com react-hook-form + Zod + shadcn/ui. Use quando criar ou modificar c\u00f3digo frontend. Use when this capability is needed.
metadata:
  author: turbo-partners
---

# Padrões Frontend

## Data Fetching com TanStack Query

### apiRequest Helper

Toda chamada API passa por `apiRequest()` em `client/src/lib/queryClient.ts`:

```typescript
import { apiRequest } from '@/lib/queryClient';

// POST
const res = await apiRequest('POST', '/api/campaigns', formData);
const campaign = await res.json();

// PUT
await apiRequest('PUT', `/api/campaigns/${id}`, updates);

// DELETE
await apiRequest('DELETE', `/api/campaigns/${id}`);
```

**Configuração global do QueryClient**:

- `staleTime: Infinity` — dados só atualizam via invalidação manual
- `refetchOnWindowFocus: false`
- `retry: false`
- `credentials: "include"` em todas as requests

### useQuery (Leitura)

```typescript
const {
  data: campaigns,
  isLoading,
  error,
} = useQuery({
  queryKey: ['/api/campaigns'], // Key = URL da API
});

// Com condição
const { data } = useQuery({ queryKey: ['/api/endpoint'], enabled: !!user });

// Auth-aware (401 retorna null)
const { data: user } = useQuery<User | null>({
  queryKey: ['/api/user'],
  queryFn: getQueryFn({ on401: 'returnNull' }),
});
```

### useMutation (Escrita)

```typescript
const queryClient = useQueryClient();

const mutation = useMutation({
  mutationFn: async (data: InsertCampaign) => {
    const res = await apiRequest('POST', '/api/campaigns', data);
    return await res.json();
  },
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['/api/campaigns'] });
    toast({ title: 'Campanha criada!' });
  },
  onError: (error: Error) => {
    toast({ title: 'Erro', description: error.message, variant: 'destructive' });
  },
});
```

### Convenções de Query Keys

```typescript
queryKey: ['/api/campaigns']; // Lista
queryKey: ['/api/campaigns', id]; // Item único
queryKey: ['/api/campaigns', { status: 'active' }]; // Com filtros
```

## Estrutura de Componentes

### Página

```typescript
// client/src/pages/campaign-details.tsx
export default function CampaignDetailsPage() {
  const { id } = useParams();  // Wouter params
  const { data, isLoading } = useQuery({ ... });

  if (isLoading) return <Skeleton className="h-40" />;
  if (!data) return <div className="text-center py-12 text-muted-foreground">Não encontrado</div>;

  return (
    <div className="container mx-auto p-6">
      <h1 className="text-2xl font-bold">{data.title}</h1>
    </div>
  );
}
```

### Componente Feature

```typescript
// client/src/components/campaign-card.tsx
interface CampaignCardProps {
  campaign: Campaign;
  onSelect?: (id: number) => void;
}

export function CampaignCard({ campaign, onSelect }: CampaignCardProps) {
  return (
    <Card className="cursor-pointer" onClick={() => onSelect?.(campaign.id)}>
      <CardHeader>
        <CardTitle>{campaign.title}</CardTitle>
      </CardHeader>
    </Card>
  );
}
```

## Routing (Wouter)

```typescript
// Rotas protegidas por role
<ProtectedRoute path="/company/dashboard" roles={["company", "admin"]}>
  <CompanyDashboard />
</ProtectedRoute>
```

Todas as páginas são lazy-loaded com `React.lazy()` + `<Suspense>`.

## Estado Global

- **MarketplaceProvider** (`client/src/lib/provider.tsx`) — `user`, `campaigns`, `applications`, `login`, `logout`
- **BrandProvider** (`client/src/lib/brand-context.tsx`) — marca selecionada (persiste no localStorage)
- **Não usar Redux/Zustand** — React Query + Context é suficiente

```typescript
const { user, campaigns, login, logout } = useMarketplace();
const { brandId, setBrandId } = useBrand();
```

## Styling

- **Tailwind CSS v4** + **cn()** de `@/lib/utils` para merge de classes
- **shadcn/ui** para componentes UI (ver skill `component-library`)
- **Não usar CSS modules** nem styled-components

---

## Formulários (React Hook Form + Zod)

### Template Completo

```typescript
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";
import { useMutation, useQueryClient } from "@tanstack/react-query";
import { apiRequest } from "@/lib/queryClient";
import { useToast } from "@/hooks/use-toast";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import {
  Form, FormControl, FormField, FormItem, FormLabel, FormMessage,
} from "@/components/ui/form";

// 1. Schema Zod (importar de @shared/schema ou definir local)
const formSchema = z.object({
  title: z.string().min(3, "Mínimo 3 caracteres").max(255),
  description: z.string().optional(),
  budget: z.coerce.number().min(0, "Budget deve ser positivo"),
});

type FormValues = z.infer<typeof formSchema>;

export function CampaignForm() {
  const { toast } = useToast();
  const queryClient = useQueryClient();

  const form = useForm<FormValues>({
    resolver: zodResolver(formSchema),
    defaultValues: { title: "", description: "", budget: 0 },
  });

  const mutation = useMutation({
    mutationFn: async (data: FormValues) => {
      const res = await apiRequest("POST", "/api/campaigns", data);
      return await res.json();
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["/api/campaigns"] });
      toast({ title: "Campanha criada!" });
      form.reset();
    },
    onError: (error: Error) => {
      toast({ title: "Erro", description: error.message, variant: "destructive" });
    },
  });

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit((data) => mutation.mutate(data))} className="space-y-4">
        <FormField control={form.control} name="title" render={({ field }) => (
          <FormItem>
            <FormLabel>Título</FormLabel>
            <FormControl><Input placeholder="Nome da campanha" {...field} /></FormControl>
            <FormMessage />
          </FormItem>
        )} />

        <Button type="submit" disabled={mutation.isPending}>
          {mutation.isPending ? "Salvando..." : "Criar Campanha"}
        </Button>
      </form>
    </Form>
  );
}
```

### Tipos de Campo

```typescript
// Input: <Input {...field} />
// Textarea: <Textarea rows={4} {...field} />
// Select:
<Select onValueChange={field.onChange} defaultValue={field.value}>
  <SelectTrigger><SelectValue placeholder="Selecione..." /></SelectTrigger>
  <SelectContent>
    <SelectItem value="draft">Rascunho</SelectItem>
  </SelectContent>
</Select>
// Checkbox: <Checkbox checked={field.value} onCheckedChange={field.onChange} />
// Switch: <Switch checked={field.value} onCheckedChange={field.onChange} />
```

### Formulário de Edição

```typescript
const { data: campaign } = useQuery({ queryKey: ['/api/campaigns', id] });

const form = useForm<FormValues>({
  resolver: zodResolver(formSchema),
  values: campaign ? { title: campaign.title, budget: campaign.budget } : undefined,
});

// PUT ao invés de POST
const mutation = useMutation({
  mutationFn: async (data: FormValues) => {
    const res = await apiRequest('PUT', `/api/campaigns/${id}`, data);
    return await res.json();
  },
});
```

### Convenções

1. **Schemas Zod compartilhados**: importar de `@shared/schema` quando existir
2. **Mensagens em português**: validações em PT-BR
3. **defaultValues obrigatório**: sempre inicializar todos os campos
4. **Toast no onSuccess/onError**: feedback visual obrigatório
5. **Invalidar queries**: sempre invalidar cache relevante após mutação
6. **Desabilitar botão**: `disabled={mutation.isPending}` durante submit

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/turbo-partners) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
