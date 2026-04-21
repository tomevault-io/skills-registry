---
name: react-dashboard-development
description: Patterns and practices for building professional financial dashboards with React/Vite/TypeScript Use when this capability is needed.
metadata:
  author: cparicardoaguirre-cell
---

# React Dashboard Development Skill

This skill provides patterns and practices for building premium financial dashboards using React, Vite, and TypeScript.

## Technology Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| Framework | React 18+ | UI components |
| Build Tool | Vite | Fast development and builds |
| Language | TypeScript | Type safety |
| Styling | CSS Custom Properties | Theming and design tokens |
| State | React hooks (useState, useEffect) | Local state management |
| UI Components | Radix UI | Accessible primitives |
| Markdown | ReactMarkdown + remark-gfm | Formatted text display |

## Project Structure

```
dashboard-project/
├── public/
│   └── data/
│       ├── financial_ratios.json
│       └── analysis_context.json
├── src/
│   ├── components/
│   │   ├── DashboardLayout.tsx
│   │   ├── FinancialAnalysis.tsx
│   │   ├── SmartRecommendations.tsx
│   │   └── Overview.tsx
│   ├── context/
│   │   └── LanguageContext.tsx
│   ├── locales/
│   │   ├── en.json
│   │   └── es.json
│   ├── App.tsx
│   ├── main.tsx
│   └── index.css
├── scripts/
│   └── extract_ratios_from_sheet.py
├── netlify/
│   └── functions/
│       └── sync-status.mts
└── package.json
```

## Design System

### Color Tokens (Dark Theme)

```css
:root {
    --bg-primary: #0a0a0f;
    --bg-secondary: #12121a;
    --bg-card: rgba(18, 18, 26, 0.95);
    --text-primary: #ffffff;
    --text-secondary: #a0a0b0;
    --accent-primary: #3b82f6;
    --accent-success: #10b981;
    --accent-warning: #f59e0b;
    --accent-danger: #ef4444;
    --border-color: rgba(255, 255, 255, 0.1);
}
```

### Card Component Pattern

```tsx
<div className="card" style={{
    background: 'var(--bg-card)',
    borderRadius: '16px',
    padding: '1.5rem',
    border: '1px solid var(--border-color)',
    backdropFilter: 'blur(10px)'
}}>
    <h3 style={{ color: 'var(--text-primary)' }}>Title</h3>
    <p style={{ color: 'var(--text-secondary)' }}>Content</p>
</div>
```

### Status Indicators

```tsx
const getStatusColor = (status: string) => {
    switch (status) {
        case 'good': return 'var(--accent-success)';
        case 'warning': return 'var(--accent-warning)';
        case 'critical': return 'var(--accent-danger)';
        default: return 'var(--text-secondary)';
    }
};
```

## Component Patterns

### Data Fetching with Fallback

```tsx
useEffect(() => {
    fetch('/data/financial_ratios.json')
        .then(res => res.json())
        .then(data => {
            setRatios(data);
            setLastSync(data.extractedAt);
        })
        .catch(err => {
            console.warn('Static load failed, trying API...', err);
            if (import.meta.env.DEV) {
                fetch('/api/financial-ratios')
                    .then(res => res.json())
                    .then(data => setRatios(data.data));
            }
        });
}, []);
```

### Bilingual Support

```tsx
// context/LanguageContext.tsx
const LanguageContext = createContext<{
    language: 'en' | 'es';
    setLanguage: (lang: 'en' | 'es') => void;
    t: (key: string) => string;
}>({ language: 'en', setLanguage: () => {}, t: (k) => k });

// Usage in component
const { language, t } = useLanguage();

return (
    <div>
        <h1>{t('dashboard.title')}</h1>
        <p>{language === 'en' ? item.description : item.descripcion}</p>
    </div>
);
```

### Smart Recommendations Pattern

```tsx
interface Recommendation {
    id: string;
    priority: 'critical' | 'high' | 'medium' | 'low';
    category: string;
    title: string;
    titleEs: string;
    issue: string;
    issueEs: string;
    action: string;
    actionEs: string;
    metrics: string[];
}

function buildRecommendations(ratios: RatioData): Recommendation[] {
    const recs: Recommendation[] = [];
    
    // Check Z-Score
    const zScore = ratios.leadingIndicators?.find(r => r.name.includes('Z-Score'));
    if (zScore && zScore.current < 1.81) {
        recs.push({
            id: 'zscore-distress',
            priority: 'critical',
            category: 'Risk',
            title: 'Bankruptcy Risk Detected',
            titleEs: 'Riesgo de Quiebra Detectado',
            issue: `Z-Score is ${zScore.current}, below safe threshold.`,
            issueEs: `Z-Score es ${zScore.current}, bajo el umbral seguro.`,
            action: 'Review cash flow and debt structure immediately.',
            actionEs: 'Revisar flujo de efectivo y estructura de deuda.',
            metrics: [`Z-Score: ${zScore.current}`, 'Safe: >2.99']
        });
    }
    
    return recs.sort((a, b) => 
        ['critical', 'high', 'medium', 'low'].indexOf(a.priority) -
        ['critical', 'high', 'medium', 'low'].indexOf(b.priority)
    );
}
```

## Animation Patterns

```css
.animate-fade-in {
    animation: fadeIn 0.5s ease-out;
}

@keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
}

.card:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.3);
    transition: all 0.3s ease;
}
```

## Environment Configuration

```typescript
// src/config.ts
const isProduction = import.meta.env.PROD;

export const config = {
    apiBaseUrl: isProduction ? '' : 'http://localhost:3001',
    isProduction,
    version: '1.0.0'
};
```

## Build and Deploy Commands

```bash
# Development
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Type checking
npx tsc --noEmit
```

## Performance Optimization

1. **Lazy load components** for large dashboards
2. **Memoize expensive calculations** with useMemo
3. **Use React.memo** for pure components
4. **Implement virtual scrolling** for long lists
5. **Optimize images** with proper formats and sizes

## Accessibility

1. Use semantic HTML elements
2. Provide proper ARIA labels
3. Ensure keyboard navigation
4. Maintain sufficient color contrast
5. Test with screen readers

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cparicardoaguirre-cell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
