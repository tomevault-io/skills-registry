---
name: security-quality-guard
description: Guardián de Calidad (Linting & Auth) - Auditor de código y seguridad Use when this capability is needed.
metadata:
  author: 0mar2090
---

# Security & Quality Guard

Actúa como auditor de código y seguridad para aplicaciones React/Next.js modernas.

## Linting con Biome

**Olvida ESLint/Prettier.** Sigue las reglas de **Biome** para linting y formateo.

### Reglas de Linting:
- ✅ Usa **Biome** como única herramienta de linting y formateo
- ✅ Si generas comandos de fix, usa: `pnpm biome check --apply`
- ❌ NO uses ESLint, Prettier ni otras herramientas de linting
- ✅ Configura Biome en `biome.json`

### Comandos de Biome:
```bash
# Verificar código
pnpm biome check .

# Aplicar fixes automáticos
pnpm biome check --apply .

# Solo formatear
pnpm biome format --write .

# Solo lint
pnpm biome lint --apply .

# CI mode (sin escribir cambios)
pnpm biome ci .
```

### Configuración Recomendada de Biome:
```json
{
  "$schema": "https://biomejs.dev/schemas/1.5.3/schema.json",
  "organizeImports": {
    "enabled": true
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true,
      "correctness": {
        "noUnusedVariables": "error",
        "useExhaustiveDependencies": "warn"
      },
      "suspicious": {
        "noExplicitAny": "error",
        "noArrayIndexKey": "warn"
      },
      "style": {
        "noNonNullAssertion": "warn",
        "useImportType": "error"
      }
    }
  },
  "formatter": {
    "enabled": true,
    "formatWithErrors": false,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineWidth": 100
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "single",
      "trailingComma": "all",
      "semicolons": "asNeeded",
      "arrowParentheses": "asNeeded"
    }
  },
  "files": {
    "ignore": [
      "node_modules",
      ".next",
      "dist",
      "build",
      ".turbo"
    ]
  }
}
```

## Autenticación y Seguridad

Protege las rutas usando **Clerk** (o **Auth.js**). Verifica siempre `auth()` en Server Components antes de devolver datos sensibles.

### Reglas de Autenticación:
- ✅ **SIEMPRE** verifica autenticación en Server Components que manejan datos sensibles
- ✅ Usa `auth()` de Clerk o `getServerSession()` de Auth.js
- ✅ Retorna `null` o redirect si no hay usuario autenticado
- ✅ Verifica permisos/roles antes de operaciones críticas
- ❌ NUNCA expongas datos sensibles sin verificar autenticación

### Ejemplo con Clerk:
```typescript
// app/dashboard/page.tsx
import { auth } from '@clerk/nextjs'
import { redirect } from 'next/navigation'

export default async function DashboardPage() {
  const { userId } = auth()
  
  if (!userId) {
    redirect('/sign-in')
  }
  
  // Ahora es seguro obtener datos del usuario
  const userData = await getUserData(userId)
  
  return <Dashboard data={userData} />
}
```

### Ejemplo con Server Actions:
```typescript
// actions/users.ts
"use server"

import { auth } from '@clerk/nextjs'
import { revalidatePath } from 'next/cache'

export async function updateUserProfile(data: UpdateProfileData) {
  const { userId } = auth()
  
  if (!userId) {
    throw new Error('No autenticado')
  }
  
  // Verificar que el usuario solo puede actualizar su propio perfil
  if (data.userId !== userId) {
    throw new Error('No autorizado')
  }
  
  const updatedUser = await db.user.update({
    where: { id: userId },
    data,
  })
  
  revalidatePath('/profile')
  return updatedUser
}
```

### Middleware de Protección de Rutas:
```typescript
// middleware.ts
import { authMiddleware } from '@clerk/nextjs'

export default authMiddleware({
  // Rutas públicas
  publicRoutes: ['/', '/about', '/pricing'],
  
  // Rutas que requieren autenticación
  // Por defecto, todas las demás rutas están protegidas
})

export const config = {
  matcher: ['/((?!.+\\.[\\w]+$|_next).*)', '/', '/(api|trpc)(.*)'],
}
```

### API Routes Seguras:
```typescript
// app/api/users/route.ts
import { auth } from '@clerk/nextjs'
import { NextResponse } from 'next/server'

export async function GET() {
  const { userId } = auth()
  
  if (!userId) {
    return NextResponse.json(
      { error: 'No autorizado' },
      { status: 401 }
    )
  }
  
  const users = await getUsers(userId)
  return NextResponse.json(users)
}

export async function POST(req: Request) {
  const { userId } = auth()
  
  if (!userId) {
    return NextResponse.json(
      { error: 'No autorizado' },
      { status: 401 }
    )
  }
  
  const body = await req.json()
  
  // Validar con Zod
  const result = userSchema.safeParse(body)
  if (!result.success) {
    return NextResponse.json(
      { error: 'Datos inválidos', details: result.error },
      { status: 400 }
    )
  }
  
  const newUser = await createUser(userId, result.data)
  return NextResponse.json(newUser, { status: 201 })
}
```

## TypeScript Estricto

Usa **TypeScript 5+**. **No uses `any`**. Usa `unknown` si es necesario y define interfaces estrictas para todas las props y respuestas de API.

### Reglas de TypeScript:
- ✅ Usa TypeScript 5+ con `strict: true`
- ❌ **NUNCA** uses `any` - esto es inaceptable
- ✅ Usa `unknown` cuando realmente no conoces el tipo
- ✅ Define interfaces/types para todas las props de componentes
- ✅ Define tipos para todas las respuestas de API
- ✅ Usa `satisfies` para type narrowing sin perder inferencia
- ✅ Habilita reglas estrictas: `noUncheckedIndexedAccess`, `noImplicitAny`, etc.

### tsconfig.json Recomendado:
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2023", "DOM", "DOM.Iterable"],
    "jsx": "preserve",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "allowJs": true,
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "incremental": true,
    "paths": {
      "@/*": ["./src/*"]
    },
    "plugins": [
      {
        "name": "next"
      }
    ]
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

### Ejemplos de Tipos Correctos:

#### ❌ INCORRECTO:
```typescript
// ❌ NO: Uso de any
function processData(data: any) {
  return data.map((item: any) => item.value)
}

// ❌ NO: Props sin tipo
function Button({ label, onClick }) {
  return <button onClick={onClick}>{label}</button>
}

// ❌ NO: Respuesta de API sin tipo
async function fetchUser(id: string) {
  const res = await fetch(`/api/users/${id}`)
  return res.json() // tipo: any
}
```

#### ✅ CORRECTO:
```typescript
// ✅ SÍ: Tipos explícitos
interface DataItem {
  id: string
  value: number
}

function processData(data: DataItem[]): number[] {
  return data.map(item => item.value)
}

// ✅ SÍ: Props tipadas
interface ButtonProps {
  label: string
  onClick: () => void
  variant?: 'primary' | 'secondary'
  disabled?: boolean
}

function Button({ label, onClick, variant = 'primary', disabled }: ButtonProps) {
  return <button onClick={onClick} disabled={disabled}>{label}</button>
}

// ✅ SÍ: Respuesta de API tipada
interface User {
  id: string
  name: string
  email: string
}

async function fetchUser(id: string): Promise<User> {
  const res = await fetch(`/api/users/${id}`)
  if (!res.ok) throw new Error('Failed to fetch user')
  return res.json() as Promise<User>
}

// ✅ MEJOR: Con validación runtime
import { z } from 'zod'

const userSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email(),
})

type User = z.infer<typeof userSchema>

async function fetchUser(id: string): Promise<User> {
  const res = await fetch(`/api/users/${id}`)
  if (!res.ok) throw new Error('Failed to fetch user')
  const data = await res.json()
  return userSchema.parse(data) // Validación runtime + type safety
}
```

### Uso Correcto de `unknown`:
```typescript
// ✅ SÍ: unknown cuando realmente no conoces el tipo
function handleError(error: unknown) {
  if (error instanceof Error) {
    console.error(error.message)
  } else if (typeof error === 'string') {
    console.error(error)
  } else {
    console.error('Error desconocido', error)
  }
}

// ✅ SÍ: Type guards
function isUser(value: unknown): value is User {
  return (
    typeof value === 'object' &&
    value !== null &&
    'id' in value &&
    'name' in value &&
    'email' in value
  )
}

function processUserData(data: unknown) {
  if (isUser(data)) {
    // Ahora TypeScript sabe que data es User
    console.log(data.name)
  }
}
```

### React Server Components con TypeScript:
```typescript
// ✅ SÍ: Props tipadas en Server Components
interface PageProps {
  params: { id: string }
  searchParams: { filter?: string }
}

export default async function UserPage({ params, searchParams }: PageProps) {
  const user = await fetchUser(params.id)
  return <UserProfile user={user} filter={searchParams.filter} />
}

// ✅ SÍ: generateMetadata tipado
import type { Metadata } from 'next'

export async function generateMetadata({ params }: PageProps): Promise<Metadata> {
  const user = await fetchUser(params.id)
  return {
    title: user.name,
    description: `Perfil de ${user.name}`,
  }
}
```

## Imports Absolutos

Prefiere **imports absolutos** usando el alias `@/` en lugar de imports relativos.

### Reglas de Imports:
- ✅ Usa `@/components/...` en lugar de `../../../components/...`
- ✅ Configura `paths` en `tsconfig.json`
- ✅ Organiza imports: externos primero, luego internos, luego tipos
- ✅ Usa `import type` para imports solo de tipos (Biome lo forzará)

### ❌ INCORRECTO:
```typescript
import Button from '../../../components/ui/Button'
import { useUser } from '../../hooks/useUser'
import { formatDate } from '../../../lib/utils'
```

### ✅ CORRECTO:
```typescript
// Externos primero
import { useState } from 'react'
import { useQuery } from '@tanstack/react-query'

// Internos con @/ alias
import { Button } from '@/components/ui/Button'
import { useUser } from '@/hooks/useUser'
import { formatDate } from '@/lib/utils'

// Tipos al final con import type
import type { User } from '@/types/user'
```

### Configuración de Path Aliases:
```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/lib/*": ["./src/lib/*"],
      "@/hooks/*": ["./src/hooks/*"],
      "@/types/*": ["./src/types/*"]
    }
  }
}
```

## Checklist de Auditoría de Código

Antes de considerar código como "listo", verifica:

### ✅ Linting & Formateo
- [ ] `pnpm biome check --apply .` ejecutado sin errores
- [ ] No hay warnings críticos de Biome
- [ ] Imports organizados correctamente

### ✅ Seguridad
- [ ] Rutas protegidas verifican `auth()` o `getServerSession()`
- [ ] Server Actions validan autenticación y autorización
- [ ] API Routes retornan 401 para usuarios no autenticados
- [ ] Datos sensibles nunca se exponen sin verificación
- [ ] Middleware configurado para rutas protegidas

### ✅ TypeScript
- [ ] No hay uso de `any` en ninguna parte
- [ ] Todas las props de componentes están tipadas
- [ ] Todas las respuestas de API están tipadas
- [ ] Todas las funciones tienen tipos de retorno explícitos
- [ ] `pnpm tsc --noEmit` pasa sin errores

### ✅ Imports
- [ ] Todos los imports usan alias `@/` cuando es apropiado
- [ ] Imports organizados: externos → internos → tipos
- [ ] `import type` usado para tipos

## Scripts Recomendados para package.json

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "biome check .",
    "lint:fix": "biome check --apply .",
    "format": "biome format --write .",
    "type-check": "tsc --noEmit",
    "ci": "pnpm type-check && pnpm biome ci .",
    "pre-commit": "pnpm lint:fix && pnpm type-check"
  }
}
```

## Ejemplo Completo de Componente Seguro y Limpio

```typescript
// components/UserProfile.tsx
'use client'

import { useState } from 'react'
import { useMutation, useQueryClient } from '@tanstack/react-query'
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'

import { Button } from '@/components/ui/Button'
import { Input } from '@/components/ui/Input'
import { updateUserProfile } from '@/actions/users'
import { userProfileSchema } from '@/schemas/user'

import type { User } from '@/types/user'
import type { z } from 'zod'

type UserProfileFormData = z.infer<typeof userProfileSchema>

interface UserProfileProps {
  user: User
}

export function UserProfile({ user }: UserProfileProps) {
  const queryClient = useQueryClient()
  const [isEditing, setIsEditing] = useState(false)

  const {
    register,
    handleSubmit,
    formState: { errors },
    reset,
  } = useForm<UserProfileFormData>({
    resolver: zodResolver(userProfileSchema),
    defaultValues: {
      name: user.name,
      email: user.email,
    },
  })

  const mutation = useMutation({
    mutationFn: updateUserProfile,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['user', user.id] })
      setIsEditing(false)
    },
  })

  const onSubmit = (data: UserProfileFormData) => {
    mutation.mutate({ ...data, userId: user.id })
  }

  return (
    <div className="space-y-4">
      {isEditing ? (
        <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
          <Input
            {...register('name')}
            label="Nombre"
            error={errors.name?.message}
            disabled={mutation.isPending}
          />
          <Input
            {...register('email')}
            type="email"
            label="Email"
            error={errors.email?.message}
            disabled={mutation.isPending}
          />
          <div className="flex gap-2">
            <Button type="submit" disabled={mutation.isPending}>
              {mutation.isPending ? 'Guardando...' : 'Guardar'}
            </Button>
            <Button
              type="button"
              variant="secondary"
              onClick={() => {
                setIsEditing(false)
                reset()
              }}
            >
              Cancelar
            </Button>
          </div>
        </form>
      ) : (
        <>
          <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
          </div>
          <Button onClick={() => setIsEditing(true)}>Editar</Button>
        </>
      )}
    </div>
  )
}
```

---

**Objetivo final:** Garantizar código limpio, seguro, type-safe y maintainable usando Biome, autenticación robusta, TypeScript estricto e imports organizados.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0mar2090) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
