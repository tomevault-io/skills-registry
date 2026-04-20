---
name: optimize-react-component
description: Optimize React components for performance following Next.js 15 and project best practices Use when this capability is needed.
metadata:
  author: kwkraus
---

When optimizing React components in this Next.js 15 dashboard:

1. **Choose the right component type**:
   - Default to Server Components (no "use client")
   - Use Client Components only when needed:
     * Using React hooks (useState, useEffect, useContext)
     * Using browser APIs (localStorage, window)
     * Using event handlers (onClick, onChange)
     * Using libraries that require client-side execution

2. **Minimize client-side JavaScript**:
   ```tsx
   // Good: Server Component (default)
   export default function StaticContent() {
     return <div>Content</div>;
   }
   
   // Use client only when necessary
   "use client";
   export default function InteractiveContent() {
     const [state, setState] = useState(false);
     return <div onClick={() => setState(!state)}>Content</div>;
   }
   ```

3. **Optimize re-renders** with React.memo and useMemo:
   ```tsx
   import { memo, useMemo } from "react";
   
   // Memoize expensive computations
   const ExpensiveComponent = memo(function ExpensiveComponent({ data }) {
     const processedData = useMemo(() => {
       return data.map(item => expensiveOperation(item));
     }, [data]);
     
     return <div>{processedData}</div>;
   });
   ```

4. **Use dynamic imports** for large components:
   ```tsx
   import dynamic from "next/dynamic";
   
   const HeavyChart = dynamic(() => import("@/components/HeavyChart"), {
     loading: () => <p>Loading chart...</p>,
     ssr: false // Disable SSR if component uses browser APIs
   });
   ```

5. **Optimize images** with next/image:
   ```tsx
   import Image from "next/image";
   
   <Image
     src="/image.png"
     alt="Description"
     width={500}
     height={300}
     priority // For above-the-fold images
   />
   ```

6. **Prevent unnecessary effect executions**:
   ```tsx
   // Include all dependencies
   useEffect(() => {
     fetchData(id);
   }, [id]); // Specify dependencies
   
   // Cleanup side effects
   useEffect(() => {
     const subscription = subscribe();
     return () => subscription.unsubscribe();
   }, []);
   ```

7. **Type safety** for better performance:
   - Use explicit TypeScript interfaces
   - Avoid `any` type
   - Enable strict mode in tsconfig.json

8. **Component splitting**:
   - Keep components focused on single responsibility
   - Extract reusable logic to custom hooks
   - Split large components into smaller ones

9. **Leverage Turbopack** (Next.js 15):
   - Use `npm run dev` which automatically uses Turbopack
   - Faster builds and hot module replacement


## Examples

### Convert client component to server component

```tsx
// Before (unnecessarily client-side)
"use client";
export default function StaticCard() {
  return <Card>Static Content</Card>;
}

// After (server component)
export default function StaticCard() {
  return <Card>Static Content</Card>;
}

```

### Optimize chart rendering

```tsx
"use client";
import { memo, useMemo } from "react";
import { LineChart, Line } from "recharts";

export const OptimizedChart = memo(function OptimizedChart({ data }) {
  const colors = useChartColors();
  
  // Memoize processed data
  const chartData = useMemo(() => {
    return data.map(item => ({
      name: item.label,
      value: item.total
    }));
  }, [data]);
  
  return (
    <LineChart data={chartData}>
      <Line dataKey="value" stroke={colors.chart1} />
    </LineChart>
  );
});

```

### Split a large component

```tsx
// Before: One large component
export default function Dashboard() {
  return (
    <div>
      <Header />
      <Stats />
      <Charts />
      <Footer />
    </div>
  );
}

// After: Split into smaller, focused components
export default function Dashboard() {
  return (
    <div>
      <DashboardHeader />
      <DashboardStats />
      <DashboardCharts />
      <DashboardFooter />
    </div>
  );
}

// Each component in its own file
// Easier to maintain, optimize, and test

```


## Related Files

- `next.config.ts`
- `tsconfig.json`
- `src/components/DashboardCharts.tsx`


## Related Skills

- `create-dashboard-page`
- `add-chart`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kwkraus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
