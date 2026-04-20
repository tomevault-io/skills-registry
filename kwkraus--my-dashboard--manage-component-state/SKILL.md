---
name: manage-component-state
description: Manage component state and data flow in React components following best practices Use when this capability is needed.
metadata:
  author: kwkraus
---

When managing state and data in this Next.js 15 dashboard:

1. **Choose the right state management approach**:
   - **Local state (useState)**: For component-specific data
   - **Context API**: For shared data across multiple components
   - **Props**: For parent-to-child data flow
   - **Server state**: For data fetching (use Server Components when possible)

2. **Local state with useState**:
   ```tsx
   "use client";
   import { useState } from "react";
   
   export default function Component() {
     const [count, setCount] = useState(0);
     const [user, setUser] = useState({ name: "", email: "" });
     
     return (
       <button onClick={() => setCount(count + 1)}>
         Count: {count}
       </button>
     );
   }
   ```

3. **Derived state** (avoid duplicate state):
   ```tsx
   // Good: Derive from props/state
   const totalRevenue = stats.reduce((sum, stat) => sum + stat.value, 0);
   
   // Avoid: Storing derived data in state
   // const [totalRevenue, setTotalRevenue] = useState(0);
   ```

4. **Complex state with useReducer**:
   ```tsx
   import { useReducer } from "react";
   
   const initialState = { count: 0, loading: false };
   
   function reducer(state, action) {
     switch (action.type) {
       case "increment":
         return { ...state, count: state.count + 1 };
       case "setLoading":
         return { ...state, loading: action.payload };
       default:
         return state;
     }
   }
   
   export default function Component() {
     const [state, dispatch] = useReducer(reducer, initialState);
     
     return (
       <button onClick={() => dispatch({ type: "increment" })}>
         {state.count}
       </button>
     );
   }
   ```

5. **Shared state with Context**:
   ```tsx
   // contexts/DashboardContext.tsx
   "use client";
   import { createContext, useContext, useState } from "react";
   
   const DashboardContext = createContext(null);
   
   export function DashboardProvider({ children }) {
     const [filters, setFilters] = useState({ date: "today" });
     
     return (
       <DashboardContext.Provider value={{ filters, setFilters }}>
         {children}
       </DashboardContext.Provider>
     );
   }
   
   export function useDashboard() {
     const context = useContext(DashboardContext);
     if (!context) throw new Error("useDashboard must be used within DashboardProvider");
     return context;
   }
   ```

6. **Side effects with useEffect**:
   ```tsx
   import { useEffect, useState } from "react";
   
   export default function Component() {
     const [data, setData] = useState([]);
     
     useEffect(() => {
       // Fetch data
       async function fetchData() {
         const response = await fetch("/api/data");
         const result = await response.json();
         setData(result);
       }
       
       fetchData();
     }, []); // Empty array = run once on mount
     
     return <div>{/* Render data */}</div>;
   }
   ```

7. **Optimizing state updates**:
   ```tsx
   // Functional updates when new state depends on old state
   setCount(prevCount => prevCount + 1);
   
   // Batch updates (automatic in React 18+)
   setCount(count + 1);
   setLoading(false);
   // Both updates batched together
   ```

8. **Form state management**:
   ```tsx
   import { useState } from "react";
   
   export default function Form() {
     const [formData, setFormData] = useState({
       name: "",
       email: "",
     });
     
     const handleChange = (e) => {
       setFormData({
         ...formData,
         [e.target.name]: e.target.value,
       });
     };
     
     const handleSubmit = (e) => {
       e.preventDefault();
       // Process formData
     };
     
     return (
       <form onSubmit={handleSubmit}>
         <input
           name="name"
           value={formData.name}
           onChange={handleChange}
         />
         <input
           name="email"
           value={formData.email}
           onChange={handleChange}
         />
         <button type="submit">Submit</button>
       </form>
     );
   }
   ```

9. **Avoid common pitfalls**:
   - Don't mutate state directly: Use spread operator or immutable updates
   - Don't call hooks conditionally
   - Don't forget cleanup in useEffect
   - Avoid storing derived data in state


## Examples

### Manage dashboard filter state

```tsx
"use client";
import { useState } from "react";

export default function Dashboard() {
  const [dateRange, setDateRange] = useState("last7days");
  const [chartType, setChartType] = useState("line");
  
  // Filtered data derived from state
  const filteredData = data.filter(item => 
    filterByDateRange(item, dateRange)
  );
  
  return (
    <div>
      <select 
        value={dateRange} 
        onChange={(e) => setDateRange(e.target.value)}
      >
        <option value="last7days">Last 7 Days</option>
        <option value="last30days">Last 30 Days</option>
      </select>
      
      <DashboardCharts data={filteredData} type={chartType} />
    </div>
  );
}

```

### Create a custom hook for data fetching

```tsx
// hooks/useStats.ts
import { useState, useEffect } from "react";

export function useStats() {
  const [stats, setStats] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    async function fetchStats() {
      try {
        const response = await fetch("/api/stats");
        const data = await response.json();
        setStats(data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    }
    
    fetchStats();
  }, []);
  
  return { stats, loading, error };
}

// Usage in component
function Dashboard() {
  const { stats, loading, error } = useStats();
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  
  return <div>{/* Render stats */}</div>;
}

```

### Manage theme state

```tsx
// This project uses next-themes for theme management
"use client";
import { useTheme } from "next-themes";

export function ThemeToggle() {
  const { theme, setTheme } = useTheme();
  
  return (
    <button onClick={() => setTheme(theme === "dark" ? "light" : "dark")}>
      Toggle Theme
    </button>
  );
}

```


## Related Files

- `src/components/theme-provider.tsx`
- `src/components/DashboardCharts.tsx`
- `src/components/LayoutWrapper.tsx`


## Related Skills

- `optimize-react-component`
- `debug-component`
- `create-dashboard-page`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kwkraus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
