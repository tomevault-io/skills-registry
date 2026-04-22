---
name: react-components
description: Build secure, performant React components in Next.js systems with TypeScript, security-first patterns, and enterprise-grade UX. Use when creating UI components, forms, providers, error boundaries, and interactive elements. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# React Components

A comprehensive skill for building secure, performant React components in Next.js 16 + React 19 App Router systems, following Fortune-500 security standards with TypeScript, accessibility, and enterprise UX patterns.

## Quick Start

Create a basic admin form component:

1. **Component Structure** (`components/admin/resource-form.tsx`):
   ```typescript
   "use client";

   import { useState } from "react";
   import { useRouter } from "next/navigation";
   import { zodResolver } from "@hookform/resolvers/zod";
   import { useForm } from "react-hook-form";
   import * as z from "zod";
   import { Button } from "@/components/ui/button";
   import { Input } from "@/components/ui/input";
   import { Form, FormControl, FormField, FormItem, FormLabel, FormMessage } from "@/components/ui/form";
   import { Card, CardContent } from "@/components/ui/card";
   import { toast } from "sonner";
   import { fetchWithCsrf } from "@/lib/fetchWithCsrf";
   import { useAdmin } from "@/hooks/useAdmin";

   const schema = z.object({
     name: z.string().min(1, "Name is required"),
     email: z.string().email("Invalid email"),
   });

   type FormValues = z.infer<typeof schema>;

   interface ResourceFormProps {
     initialData?: { id: string; name: string; email: string };
   }

   export function ResourceForm({ initialData }: ResourceFormProps) {
     const router = useRouter();
     const { hasPermission } = useAdmin();
     const [loading, setLoading] = useState(false);

     const form = useForm<FormValues>({
       resolver: zodResolver(schema),
       defaultValues: {
         name: initialData?.name || "",
         email: initialData?.email || "",
       },
     });

     const onSubmit = async (data: FormValues) => {
       try {
         setLoading(true);
         const endpoint = initialData ? `/api/admin/resources/${initialData.id}` : "/api/admin/resources";
         const method = initialData ? "PATCH" : "POST";

         const res = await fetchWithCsrf(endpoint, {
           method,
           body: JSON.stringify(data),
         });

         if (!res.ok) throw new Error("Failed to save");

         toast.success(initialData ? "Resource updated" : "Resource created");
         router.refresh();
         router.push("/admin/resources");
       } catch (error) {
         toast.error("Something went wrong");
       } finally {
         setLoading(false);
       }
     };

     return (
       <Form {...form}>
         <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-8">
           <Card>
             <CardContent className="pt-6">
               <div className="grid gap-4 md:grid-cols-2">
                 <FormField
                   control={form.control}
                   name="name"
                   render={({ field }) => (
                     <FormItem>
                       <FormLabel>Name</FormLabel>
                       <FormControl>
                         <Input placeholder="Enter name" {...field} />
                       </FormControl>
                       <FormMessage />
                     </FormItem>
                   )}
                 />
                 <FormField
                   control={form.control}
                   name="email"
                   render={({ field }) => (
                     <FormItem>
                       <FormLabel>Email</FormLabel>
                       <FormControl>
                         <Input placeholder="email@example.com" {...field} />
                       </FormControl>
                       <FormMessage />
                     </FormItem>
                   )}
                 />
               </div>
               <div className="flex justify-end gap-4 mt-6">
                 <Button
                   type="button"
                   variant="outline"
                   onClick={() => router.push("/admin/resources")}
                   disabled={loading}
                 >
                   Cancel
                 </Button>
                 <Button type="submit" disabled={loading}>
                   {loading ? "Saving..." : initialData ? "Update" : "Create"}
                 </Button>
               </div>
             </CardContent>
           </Card>
         </form>
       </Form>
     );
   }
   ```

2. **UI Component** (`components/ui/custom-button.tsx`):
   ```typescript
   import * as React from "react";
   import { Slot } from "@radix-ui/react-slot";
   import { cva, type VariantProps } from "class-variance-authority";
   import { cn } from "@/lib/utils";

   const customButtonVariants = cva(
     "inline-flex items-center justify-center whitespace-nowrap rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50",
     {
       variants: {
         variant: {
           default: "bg-primary text-primary-foreground hover:bg-primary/90",
           destructive: "bg-destructive text-destructive-foreground hover:bg-destructive/90",
           outline: "border border-input bg-background hover:bg-accent hover:text-accent-foreground",
           premium: "bg-gradient-to-r from-primary to-primary/80 text-primary-foreground hover:opacity-90 shadow-lg",
         },
         size: {
           default: "h-10 px-4 py-2",
           sm: "h-9 rounded-md px-3",
           lg: "h-12 rounded-md px-8 text-base",
           icon: "h-10 w-10",
         },
       },
       defaultVariants: {
         variant: "default",
         size: "default",
       },
     }
   );

   export interface CustomButtonProps
     extends React.ButtonHTMLAttributes<HTMLButtonElement>,
       VariantProps<typeof customButtonVariants> {
     asChild?: boolean;
   }

   const CustomButton = React.forwardRef<HTMLButtonElement, CustomButtonProps>(
     ({ className, variant, size, asChild = false, ...props }, ref) => {
       const Comp = asChild ? Slot : "button";
       return (
         <Comp
           className={cn(customButtonVariants({ variant, size, className }))}
           ref={ref}
           {...props}
         />
       );
     }
   );
   CustomButton.displayName = "CustomButton";

   export { CustomButton, customButtonVariants };
   ```

## Core Concepts

### Component Architecture

- **Client Components**: Use `"use client"` directive for interactive components
- **Server Components**: Default for static content and data fetching
- **Provider Pattern**: Wrap app with context providers (Theme, Auth, Motion)
- **Composition**: Use Radix Slot for flexible component composition

### Security Integration

- **Permission Gating**: `useAdmin().hasPermission()` for UI controls
- **CSRF Protection**: `fetchWithCsrf()` for secure API calls
- **Safe DTOs**: Type-safe data handling with `SafeUserWithRolesDto`
- **Error Boundaries**: Class-based components for error isolation

### Form Handling

- **React Hook Form + Zod**: Type-safe validation and form state
- **Progressive Enhancement**: Client-side validation with server enforcement
- **Loading States**: Proper UX during async operations
- **Toast Notifications**: User feedback with Sonner

### Styling Patterns

- **Tailwind CSS**: Utility-first styling with custom design system
- **Class Variance Authority**: Type-safe component variants
- **Responsive Design**: Mobile-first with `md:` breakpoints
- **Dark Mode**: Theme-aware styling with next-themes

## Workflows

### 1. Creating Provider Components

**Purpose**: Set up application-wide context and configuration.

**Steps**:
1. Create provider wrapper component
2. Define TypeScript interfaces
3. Handle client-side logic appropriately
4. Export for app layout integration

**Example**: Theme Provider
```typescript
"use client";

import * as React from "react";
import { ThemeProvider as NextThemesProvider } from "next-themes";
import type { ThemeProviderProps } from "next-themes/dist/types";

export function ThemeProvider({ children, ...props }: ThemeProviderProps) {
  return <NextThemesProvider {...props}>{children}</NextThemesProvider>;
}
```

### 2. Building Admin Forms

**Purpose**: Create secure forms for administrative operations.

**Steps**:
1. Define Zod schema for validation
2. Set up React Hook Form with zodResolver
3. Add permission checks with useAdmin hook
4. Implement CSRF-aware submission
5. Handle loading states and navigation

**Example**: User Management Form
```typescript
export function UserForm({ initialData, roles }: UserFormProps) {
  const { hasPermission } = useAdmin();
  const canSubmit = initialData ? hasPermission("users.edit") : hasPermission("users.create");

  const form = useForm<UserFormValues>({
    resolver: zodResolver(userSchema),
    defaultValues: {
      name: initialData?.name || "",
      email: initialData?.email || "",
      roleIds: initialData?.RoleAssignment?.map((r) => r.roleId) || [],
    },
  });

  const onSubmit = async (data: UserFormValues) => {
    const res = await fetchWithCsrf("/api/admin/users", {
      method: "POST",
      body: JSON.stringify(data),
    });

    if (res.ok) {
      toast.success("User created");
      router.refresh();
      router.push("/admin/users");
    }
  };

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)}>
        {/* Form fields with permission gating */}
        <Button disabled={!canSubmit}>Create User</Button>
      </form>
    </Form>
  );
}
```

### 3. Implementing Error Boundaries

**Purpose**: Handle component errors gracefully with custom UI.

**Steps**:
1. Create class-based component extending React.Component
2. Implement getDerivedStateFromError and componentDidCatch
3. Provide fallback UI with recovery options
4. Log errors for monitoring

**Example**: Custom Error Boundary
```typescript
interface State {
  hasError: boolean;
  error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
  public state: State = {
    hasError: false,
    error: null,
  };

  public static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  public componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error("Component error:", error, errorInfo);
  }

  private handleReload = () => {
    window.location.reload();
  };

  public render() {
    if (this.state.hasError) {
      return (
        <div className="flex flex-col items-center justify-center min-h-[50vh] p-6">
          <h2 className="text-2xl font-bold">System Error</h2>
          <p>An unexpected error occurred.</p>
          <Button onClick={this.handleReload}>Reload</Button>
        </div>
      );
    }

    return this.props.children;
  }
}
```

### 4. Creating Reusable UI Components

**Purpose**: Build consistent, accessible UI primitives.

**Steps**:
1. Use class-variance-authority for variants
2. Implement Radix Slot for composition
3. Add proper TypeScript interfaces
4. Include accessibility attributes
5. Support responsive design

**Example**: Custom Card Component
```typescript
import { cva, type VariantProps } from "class-variance-authority";
import { cn } from "@/lib/utils";

const cardVariants = cva(
  "rounded-lg border bg-card text-card-foreground shadow-sm",
  {
    variants: {
      variant: {
        default: "",
        elevated: "shadow-lg",
        outlined: "border-2",
      },
      padding: {
        none: "",
        sm: "p-4",
        md: "p-6",
        lg: "p-8",
      },
    },
    defaultVariants: {
      variant: "default",
      padding: "md",
    },
  }
);

export interface CardProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof cardVariants> {}

const Card = React.forwardRef<HTMLDivElement, CardProps>(
  ({ className, variant, padding, ...props }, ref) => (
    <div
      ref={ref}
      className={cn(cardVariants({ variant, padding, className }))}
      {...props}
    />
  )
);
Card.displayName = "Card";

export { Card, cardVariants };
```

## Advanced Features

### Motion and Animation

Configure Framer Motion for reduced motion support:

```typescript
"use client";

import { MotionConfig } from "framer-motion";

export function AppMotionConfig({ children }: { children: React.ReactNode }) {
  return <MotionConfig reducedMotion="user">{children}</MotionConfig>;
}
```

### Context Providers

Authentication provider with NextAuth:

```typescript
"use client";

import { SessionProvider } from "next-auth/react";

interface AuthProviderProps {
  children: ReactNode;
  session: any;
}

export function AuthProvider({ children, session }: AuthProviderProps) {
  return <SessionProvider session={session}>{children}</SessionProvider>;
}
```

### Custom Hooks Integration

Admin permissions hook:

```typescript
import { useSession } from "next-auth/react";

export function useAdmin() {
  const { data: session } = useSession();

  const hasPermission = (permission: string) => {
    return session?.user?.permissions?.includes(permission) ?? false;
  };

  return { hasPermission, user: session?.user };
}
```

## Troubleshooting

### Common Issues

**Hydration Mismatches**: Ensure server and client render the same content. Use `useEffect` for client-only logic.

**Permission Denied**: Verify user has required permissions and `useAdmin` hook is properly configured.

**CSRF Errors**: Always use `fetchWithCsrf` for admin API calls, never regular `fetch`.

**Form Validation**: Check Zod schema matches form field names and types.

**Styling Issues**: Ensure Tailwind classes are properly configured and variants are defined.

## Best Practices

- Always use `"use client"` for interactive components
- Implement proper TypeScript interfaces with JSDoc comments
- Use Zod schemas for all form validation
- Include permission checks for admin components
- Handle loading states and error cases
- Use consistent naming conventions
- Implement accessibility features
- Test components with different user permissions

## Examples

See the codebase for complete implementations:
- [`components/error-boundary.tsx`](components/error-boundary.tsx:1) - Error boundary component
- [`components/theme-provider.tsx`](components/theme-provider.tsx:1) - Theme provider wrapper
- [`components/auth-provider.tsx`](components/auth-provider.tsx:1) - Authentication provider
- [`components/admin/users/user-form.tsx`](components/admin/users/user-form.tsx:1) - Admin form with security
- [`components/ui/button.tsx`](components/ui/button.tsx:1) - UI component with variants
- [`components/motion-config.tsx`](components/motion-config.tsx:1) - Motion configuration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
