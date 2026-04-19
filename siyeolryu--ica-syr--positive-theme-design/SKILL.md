---
name: positive-theme-design
description: 긍정적이고 활기찬 테마 디자인 시스템. 주식 브리핑 서비스에 신뢰감과 희망, 성장의 메시지를 전달하는 UI/UX 설계. Use when this capability is needed.
metadata:
  author: siyeolryu
---

# Positive Theme Design System

"당신이 잠든 사이" 프로젝트를 위한 긍정적이고 활력 넘치는 디자인 시스템입니다.

## 디자인 철학

### 핵심 가치
- **신뢰 (Trust)**: 전문적이고 안정적인 정보 제공
- **희망 (Hope)**: 밝은 미래와 성장 가능성
- **활력 (Energy)**: 생동감 있고 역동적인 경험
- **친근함 (Approachability)**: 누구나 쉽게 이해하고 사용

### 감성 키워드
아침, 일출, 새로운 시작, 성장, 기회, 성공, 긍정, 희망, 전문성, 신뢰

---

## 컬러 시스템

### Primary Colors (주색상)

#### Sky Blue - 신뢰와 전문성
```css
--primary-50: #f0f9ff    /* 하늘빛 배경 */
--primary-100: #e0f2fe   /* 부드러운 강조 */
--primary-200: #bae6fd   /* 밝은 요소 */
--primary-300: #7dd3fc   /* 호버 효과 */
--primary-400: #38bdf8   /* 메인 액션 */
--primary-500: #0ea5e9   /* 주요 버튼, 링크 */
--primary-600: #0284c7   /* 진한 액센트 */
--primary-700: #0369a1   /* 강조 텍스트 */
--primary-800: #075985   /* 다크 모드 주색 */
--primary-900: #0c4a6e   /* 최고 강조 */
```

**사용 예시**:
- 메인 버튼, CTA
- 링크, 활성 네비게이션
- 강조 아이콘
- 로딩 인디케이터

#### Golden Sunrise - 성장과 기회
```css
--secondary-50: #fffbeb   /* 따뜻한 배경 */
--secondary-100: #fef3c7  /* 부드러운 하이라이트 */
--secondary-200: #fde68a  /* 밝은 강조 */
--secondary-300: #fcd34d  /* 황금빛 */
--secondary-400: #fbbf24  /* 메인 골드 */
--secondary-500: #f59e0b  /* 주요 액센트 */
--secondary-600: #d97706  /* 진한 골드 */
--secondary-700: #b45309  /* 강조 요소 */
--secondary-800: #92400e  /* 다크 모드 골드 */
--secondary-900: #78350f  /* 최고 강조 */
```

**사용 예시**:
- 중요 알림, 배지
- 프리미엄 기능 강조
- 성공 메시지
- 화제 종목 하이라이트

#### Mint Fresh - 신선함과 긍정
```css
--accent-50: #f0fdfa    /* 민트 배경 */
--accent-100: #ccfbf1   /* 부드러운 민트 */
--accent-200: #99f6e4   /* 밝은 민트 */
--accent-300: #5eead4   /* 터콰이즈 */
--accent-400: #2dd4bf   /* 메인 민트 */
--accent-500: #14b8a6   /* 주요 액센트 */
--accent-600: #0d9488   /* 진한 민트 */
--accent-700: #0f766e   /* 강조 요소 */
--accent-800: #115e59   /* 다크 모드 민트 */
--accent-900: #134e4a   /* 최고 강조 */
```

**사용 예시**:
- 뉴스 카드 강조
- 새로운 기능 표시
- 부가 정보 하이라이트
- 통계 그래프 보조 색상

### Background Colors (배경색)

#### Light Mode - 따뜻한 아침 햇살
```css
/* 기본 배경 */
--bg-primary: #ffffff           /* 순백색 */
--bg-secondary: #fefefe         /* 아이보리 화이트 */
--bg-tertiary: #f8fafc          /* 연한 회색 */

/* 카드 배경 - 미묘한 그라데이션 */
--card-bg: linear-gradient(135deg, #ffffff 0%, #f0f9ff 100%)
--card-hover-bg: linear-gradient(135deg, #ffffff 0%, #e0f2fe 100%)

/* 섹션 배경 */
--section-bg-warm: #fffbeb      /* 따뜻한 배경 */
--section-bg-cool: #f0f9ff      /* 시원한 배경 */
--section-bg-fresh: #f0fdfa     /* 신선한 배경 */
```

#### Dark Mode - 고요한 밤하늘
```css
/* 기본 배경 - slate 대신 네이비/보라 계열 */
--bg-primary: #0f0e1e           /* 깊은 네이비 */
--bg-secondary: #1a1828         /* 부드러운 보라 */
--bg-tertiary: #252238          /* 밝은 네이비 */

/* 카드 배경 */
--card-bg: linear-gradient(135deg, #1a1828 0%, #1e2747 100%)
--card-hover-bg: linear-gradient(135deg, #1e2747 0%, #2d3561 100%)

/* 섹션 배경 */
--section-bg-warm: #2a1f1a      /* 따뜻한 밤 */
--section-bg-cool: #1a2332      /* 시원한 밤 */
--section-bg-fresh: #1a2b2a     /* 신선한 밤 */
```

### Semantic Colors (의미 색상)

#### Stock Price Colors - 명확한 시각적 피드백
```css
/* 상승 - 생기 있는 초록 */
--stock-up: #10b981           /* Emerald 500 */
--stock-up-bg: #d1fae5        /* Emerald 100 */
--stock-up-dark: #047857      /* Emerald 700 */

/* 하락 - 명확한 빨강 (과하지 않게) */
--stock-down: #f43f5e         /* Rose 500 */
--stock-down-bg: #ffe4e6      /* Rose 100 */
--stock-down-dark: #be123c    /* Rose 700 */

/* 보합 - 중립적인 회색 */
--stock-neutral: #64748b      /* Slate 500 */
--stock-neutral-bg: #f1f5f9   /* Slate 100 */
```

#### Status Colors - 명확한 피드백
```css
/* 성공 */
--success: #10b981            /* Green */
--success-bg: #d1fae5
--success-border: #6ee7b7

/* 경고 */
--warning: #f59e0b            /* Amber */
--warning-bg: #fef3c7
--warning-border: #fcd34d

/* 위험 */
--danger: #ef4444             /* Red */
--danger-bg: #fee2e2
--danger-border: #fca5a5

/* 정보 */
--info: #3b82f6               /* Blue */
--info-bg: #dbeafe
--info-border: #93c5fd
```

### Text Colors (텍스트 색상)

#### Light Mode
```css
--text-primary: #1e293b       /* Slate 800 - 주요 텍스트 */
--text-secondary: #475569     /* Slate 600 - 보조 텍스트 */
--text-tertiary: #94a3b8      /* Slate 400 - 부가 정보 */
--text-accent: #0ea5e9        /* Primary 500 - 강조 텍스트 */
```

#### Dark Mode
```css
--text-primary: #f1f5f9       /* Slate 100 - 주요 텍스트 */
--text-secondary: #cbd5e1     /* Slate 300 - 보조 텍스트 */
--text-tertiary: #64748b      /* Slate 500 - 부가 정보 */
--text-accent: #38bdf8        /* Primary 400 - 강조 텍스트 */
```

### Border & Shadow

#### Borders
```css
/* Light Mode */
--border-light: #e2e8f0       /* Slate 200 */
--border-medium: #cbd5e1      /* Slate 300 */
--border-heavy: #94a3b8       /* Slate 400 */

/* Dark Mode */
--border-dark-light: #334155  /* Slate 700 */
--border-dark-medium: #475569 /* Slate 600 */
--border-dark-heavy: #64748b  /* Slate 500 */
```

#### Shadows - 깊이감과 부드러움
```css
/* Light Mode */
--shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05)
--shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06)
--shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05)
--shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04)
--shadow-glow: 0 0 20px rgba(14, 165, 233, 0.3) /* 주색상 글로우 */

/* Dark Mode */
--shadow-dark-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.3)
--shadow-dark-md: 0 4px 6px -1px rgba(0, 0, 0, 0.4), 0 2px 4px -1px rgba(0, 0, 0, 0.3)
--shadow-dark-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.5), 0 4px 6px -2px rgba(0, 0, 0, 0.4)
--shadow-dark-glow: 0 0 20px rgba(56, 189, 248, 0.2)
```

---

## 타이포그래피

### 폰트 패밀리

#### 한글 폰트
```css
/* Primary - 본문용 */
--font-primary: 'Pretendard', -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif

/* Display - 제목용 */
--font-display: 'Pretendard', -apple-system, BlinkMacSystemFont, sans-serif

/* Mono - 숫자/코드 */
--font-mono: 'JetBrains Mono', 'Fira Code', 'SF Mono', monospace
```

#### 영문 폰트
```css
--font-en-primary: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif
--font-en-display: 'Poppins', sans-serif
```

### 폰트 크기 & 행간

```css
/* Display - 큰 제목 */
--text-display-xl: 3.75rem    /* 60px - h1 */
--text-display-lg: 3rem       /* 48px - h1 */
--text-display-md: 2.25rem    /* 36px - h2 */

/* Heading - 제목 */
--text-heading-lg: 1.875rem   /* 30px - h2 */
--text-heading-md: 1.5rem     /* 24px - h3 */
--text-heading-sm: 1.25rem    /* 20px - h4 */

/* Body - 본문 */
--text-body-lg: 1.125rem      /* 18px */
--text-body-md: 1rem          /* 16px - 기본 */
--text-body-sm: 0.875rem      /* 14px */
--text-body-xs: 0.75rem       /* 12px */

/* Line Height */
--leading-tight: 1.25
--leading-normal: 1.5
--leading-relaxed: 1.75
--leading-loose: 2
```

### 폰트 굵기

```css
--font-light: 300
--font-normal: 400
--font-medium: 500
--font-semibold: 600
--font-bold: 700
--font-extrabold: 800
```

### 제목 스타일 - 그라데이션 효과

```css
/* 그라데이션 텍스트 - 메인 제목 */
.heading-gradient {
  background: linear-gradient(135deg, #0ea5e9 0%, #8b5cf6 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

/* 그라데이션 텍스트 - 골드 강조 */
.heading-gradient-gold {
  background: linear-gradient(135deg, #f59e0b 0%, #ef4444 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
```

---

## 레이아웃 & 스페이싱

### Container Widths
```css
--container-sm: 640px
--container-md: 768px
--container-lg: 1024px
--container-xl: 1280px
--container-2xl: 1536px
```

### Spacing Scale
```css
--space-1: 0.25rem    /* 4px */
--space-2: 0.5rem     /* 8px */
--space-3: 0.75rem    /* 12px */
--space-4: 1rem       /* 16px */
--space-5: 1.25rem    /* 20px */
--space-6: 1.5rem     /* 24px */
--space-8: 2rem       /* 32px */
--space-10: 2.5rem    /* 40px */
--space-12: 3rem      /* 48px */
--space-16: 4rem      /* 64px */
--space-20: 5rem      /* 80px */
--space-24: 6rem      /* 96px */
```

### Border Radius
```css
--radius-sm: 0.375rem   /* 6px */
--radius-md: 0.5rem     /* 8px */
--radius-lg: 0.75rem    /* 12px */
--radius-xl: 1rem       /* 16px */
--radius-2xl: 1.5rem    /* 24px */
--radius-full: 9999px   /* 원형 */
```

### Grid System
```css
/* 12-column grid */
.grid-12 {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  gap: 1.5rem;
}

/* Responsive columns */
.col-1 { grid-column: span 1; }
.col-2 { grid-column: span 2; }
.col-3 { grid-column: span 3; }
.col-4 { grid-column: span 4; }
.col-6 { grid-column: span 6; }
.col-12 { grid-column: span 12; }
```

---

## 컴포넌트 스타일

### Card - 입체감과 생동감

```css
/* Light Mode Card */
.card {
  background: linear-gradient(135deg, #ffffff 0%, #f0f9ff 100%);
  border: 1px solid rgba(226, 232, 240, 0.8);
  border-radius: 1rem;
  padding: 1.5rem;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.card:hover {
  background: linear-gradient(135deg, #ffffff 0%, #e0f2fe 100%);
  box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1),
              0 0 20px rgba(14, 165, 233, 0.2);
  transform: translateY(-4px);
}

/* Dark Mode Card */
.dark .card {
  background: linear-gradient(135deg, #1a1828 0%, #1e2747 100%);
  border: 1px solid rgba(71, 85, 105, 0.5);
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.4);
}

.dark .card:hover {
  background: linear-gradient(135deg, #1e2747 0%, #2d3561 100%);
  box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.5),
              0 0 20px rgba(56, 189, 248, 0.15);
}
```

### Button - 명확하고 매력적인

```css
/* Primary Button */
.btn-primary {
  background: linear-gradient(135deg, #0ea5e9 0%, #0284c7 100%);
  color: white;
  font-weight: 600;
  padding: 0.75rem 1.5rem;
  border-radius: 0.75rem;
  border: none;
  box-shadow: 0 4px 6px -1px rgba(14, 165, 233, 0.3);
  transition: all 0.2s ease;
}

.btn-primary:hover {
  background: linear-gradient(135deg, #0284c7 0%, #0369a1 100%);
  box-shadow: 0 10px 15px -3px rgba(14, 165, 233, 0.4);
  transform: translateY(-2px);
}

.btn-primary:active {
  transform: translateY(0);
}

/* Secondary Button */
.btn-secondary {
  background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%);
  color: white;
  /* ... 나머지 동일 */
}

/* Ghost Button */
.btn-ghost {
  background: transparent;
  color: var(--primary-600);
  border: 2px solid var(--primary-400);
  /* ... */
}
```

### Input Fields - 친근하고 명확한

```css
.input {
  background: var(--bg-primary);
  border: 2px solid var(--border-light);
  border-radius: 0.75rem;
  padding: 0.75rem 1rem;
  font-size: 1rem;
  transition: all 0.2s ease;
}

.input:focus {
  outline: none;
  border-color: var(--primary-400);
  box-shadow: 0 0 0 3px rgba(14, 165, 233, 0.1);
}

.dark .input {
  background: var(--bg-secondary);
  border-color: var(--border-dark-light);
  color: var(--text-primary);
}
```

### Badge & Pills - 눈에 띄는 정보

```css
/* Primary Badge */
.badge-primary {
  background: linear-gradient(135deg, #bae6fd 0%, #7dd3fc 100%);
  color: var(--primary-700);
  padding: 0.25rem 0.75rem;
  border-radius: 9999px;
  font-size: 0.875rem;
  font-weight: 600;
}

/* Gold Badge - 중요 정보 */
.badge-gold {
  background: linear-gradient(135deg, #fde68a 0%, #fbbf24 100%);
  color: var(--secondary-800);
}

/* Success Badge */
.badge-success {
  background: #d1fae5;
  color: #065f46;
}
```

---

## 애니메이션 & 인터랙션

### Transition Timing
```css
--transition-fast: 150ms
--transition-normal: 200ms
--transition-slow: 300ms
--transition-slower: 500ms

--easing-smooth: cubic-bezier(0.4, 0, 0.2, 1)
--easing-bounce: cubic-bezier(0.68, -0.55, 0.265, 1.55)
--easing-elastic: cubic-bezier(0.175, 0.885, 0.32, 1.275)
```

### Hover Effects

```css
/* Card Lift */
.hover-lift {
  transition: transform 0.3s var(--easing-smooth),
              box-shadow 0.3s var(--easing-smooth);
}

.hover-lift:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow-xl);
}

/* Glow Effect */
.hover-glow {
  transition: box-shadow 0.3s ease;
}

.hover-glow:hover {
  box-shadow: 0 0 20px rgba(14, 165, 233, 0.4);
}

/* Scale */
.hover-scale {
  transition: transform 0.2s var(--easing-smooth);
}

.hover-scale:hover {
  transform: scale(1.05);
}
```

### Loading States

```css
/* Spinner */
.spinner {
  border: 3px solid rgba(14, 165, 233, 0.2);
  border-top-color: var(--primary-500);
  border-radius: 50%;
  width: 2rem;
  height: 2rem;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

/* Skeleton */
.skeleton {
  background: linear-gradient(
    90deg,
    var(--bg-tertiary) 0%,
    var(--border-light) 50%,
    var(--bg-tertiary) 100%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s ease-in-out infinite;
}

@keyframes shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
```

### Fade & Slide Animations

```css
/* Fade In */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.fade-in {
  animation: fadeIn 0.5s var(--easing-smooth);
}

/* Slide Up */
@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.slide-up {
  animation: slideUp 0.5s var(--easing-smooth);
}
```

---

## Chart Styling (Recharts)

### Line Chart - 명확하고 아름다운

```typescript
// 컬러 팔레트
const chartColors = {
  primary: '#0ea5e9',     // Sky Blue
  secondary: '#f59e0b',   // Golden
  accent: '#14b8a6',      // Mint
  up: '#10b981',          // Green
  down: '#f43f5e',        // Rose
};

// 그라데이션 정의
<defs>
  <linearGradient id="colorPrice" x1="0" y1="0" x2="0" y2="1">
    <stop offset="5%" stopColor={chartColors.primary} stopOpacity={0.3} />
    <stop offset="95%" stopColor={chartColors.primary} stopOpacity={0} />
  </linearGradient>
</defs>

// Line 스타일
<Line
  type="monotone"
  dataKey="price"
  stroke={chartColors.primary}
  strokeWidth={3}
  fill="url(#colorPrice)"
  dot={false}
  activeDot={{ r: 6, fill: chartColors.primary }}
/>
```

### Dark Mode Chart Styling

```typescript
const { theme } = useTheme();

const axisColor = theme === 'dark' ? '#cbd5e1' : '#64748b';
const gridColor = theme === 'dark' ? '#334155' : '#e2e8f0';

<XAxis
  stroke={axisColor}
  tick={{ fill: axisColor }}
  style={{ fontSize: '0.875rem' }}
/>

<YAxis
  stroke={axisColor}
  tick={{ fill: axisColor }}
/>

<CartesianGrid
  strokeDasharray="3 3"
  stroke={gridColor}
  opacity={0.3}
/>

<Tooltip
  contentStyle={{
    backgroundColor: theme === 'dark' ? '#1a1828' : '#ffffff',
    border: 'none',
    borderRadius: '0.75rem',
    boxShadow: theme === 'dark'
      ? '0 10px 15px -3px rgba(0, 0, 0, 0.5)'
      : '0 10px 15px -3px rgba(0, 0, 0, 0.1)',
  }}
  labelStyle={{
    color: theme === 'dark' ? '#f1f5f9' : '#1e293b',
    fontWeight: 600,
  }}
/>
```

---

## 사용 예시

### 메인 대시보드 헤더

```tsx
<div className="relative overflow-hidden bg-gradient-to-br from-primary-50 via-white to-secondary-50 dark:from-[#0f0e1e] dark:via-[#1a1828] dark:to-[#1e2747] rounded-2xl p-8 mb-8">
  {/* 배경 장식 요소 */}
  <div className="absolute top-0 right-0 w-64 h-64 bg-primary-200/20 dark:bg-primary-800/10 rounded-full blur-3xl" />
  <div className="absolute bottom-0 left-0 w-48 h-48 bg-secondary-200/20 dark:bg-secondary-800/10 rounded-full blur-3xl" />

  {/* 내용 */}
  <div className="relative z-10">
    <h1 className="text-4xl md:text-5xl font-bold mb-3">
      <span className="bg-gradient-to-r from-primary-600 to-purple-600 bg-clip-text text-transparent">
        좋은 아침입니다!
      </span>
    </h1>
    <p className="text-lg text-slate-600 dark:text-slate-300">
      오늘도 새로운 기회가 기다리고 있습니다
    </p>
  </div>
</div>
```

### 주가 카드 - 긍정적 강조

```tsx
<div className="group card hover:shadow-2xl hover:scale-[1.02] transition-all duration-300">
  {/* 상단 헤더 */}
  <div className="flex items-start justify-between mb-4">
    <div>
      <h3 className="text-2xl font-bold bg-gradient-to-r from-slate-900 to-primary-700 dark:from-slate-100 dark:to-primary-400 bg-clip-text text-transparent">
        {stock.name}
      </h3>
      <p className="text-slate-500 dark:text-slate-400">{stock.symbol}</p>
    </div>

    {/* 변동률 배지 - 동적 색상 */}
    <div className={`
      px-4 py-2 rounded-full font-semibold
      ${isPositive
        ? 'bg-gradient-to-r from-emerald-50 to-emerald-100 dark:from-emerald-900/30 dark:to-emerald-800/30 text-emerald-700 dark:text-emerald-400'
        : 'bg-gradient-to-r from-rose-50 to-rose-100 dark:from-rose-900/30 dark:to-rose-800/30 text-rose-700 dark:text-rose-400'
      }
    `}>
      {isPositive ? '↗' : '↘'} {Math.abs(stock.changePercent).toFixed(2)}%
    </div>
  </div>

  {/* 차트 */}
  <div className="my-6">
    <MiniStockChart data={chartData} isPositive={isPositive} />
  </div>

  {/* 가격 정보 */}
  <div className="flex items-baseline gap-2">
    <span className="text-3xl font-bold text-slate-900 dark:text-white">
      ${stock.price.toFixed(2)}
    </span>
    <span className={`text-lg font-semibold ${isPositive ? 'text-emerald-600 dark:text-emerald-400' : 'text-rose-600 dark:text-rose-400'}`}>
      {isPositive ? '+' : ''}{stock.change.toFixed(2)}
    </span>
  </div>
</div>
```

### 버튼 그룹

```tsx
<div className="flex gap-3">
  {/* Primary Action */}
  <button className="
    px-6 py-3 rounded-xl font-semibold text-white
    bg-gradient-to-r from-primary-500 to-primary-600
    hover:from-primary-600 hover:to-primary-700
    shadow-lg shadow-primary-500/30
    hover:shadow-xl hover:shadow-primary-500/40
    hover:-translate-y-0.5
    active:translate-y-0
    transition-all duration-200
  ">
    브리핑 생성하기
  </button>

  {/* Secondary Action */}
  <button className="
    px-6 py-3 rounded-xl font-semibold
    bg-white dark:bg-slate-800
    text-primary-600 dark:text-primary-400
    border-2 border-primary-300 dark:border-primary-700
    hover:bg-primary-50 dark:hover:bg-primary-900/20
    hover:-translate-y-0.5
    active:translate-y-0
    transition-all duration-200
  ">
    히스토리 보기
  </button>
</div>
```

---

## Tailwind Config 적용

### tailwind.config.js

```javascript
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        // Primary - Sky Blue
        primary: {
          50: '#f0f9ff',
          100: '#e0f2fe',
          200: '#bae6fd',
          300: '#7dd3fc',
          400: '#38bdf8',
          500: '#0ea5e9',
          600: '#0284c7',
          700: '#0369a1',
          800: '#075985',
          900: '#0c4a6e',
        },
        // Secondary - Golden
        secondary: {
          50: '#fffbeb',
          100: '#fef3c7',
          200: '#fde68a',
          300: '#fcd34d',
          400: '#fbbf24',
          500: '#f59e0b',
          600: '#d97706',
          700: '#b45309',
          800: '#92400e',
          900: '#78350f',
        },
        // Accent - Mint
        accent: {
          50: '#f0fdfa',
          100: '#ccfbf1',
          200: '#99f6e4',
          300: '#5eead4',
          400: '#2dd4bf',
          500: '#14b8a6',
          600: '#0d9488',
          700: '#0f766e',
          800: '#115e59',
          900: '#134e4a',
        },
        // Stock Colors
        'stock-up': '#10b981',
        'stock-down': '#f43f5e',
        // Background Colors
        'light-bg': '#ffffff',
        'light-card': '#fefefe',
        'light-border': '#e2e8f0',
        'dark-bg': '#0f0e1e',
        'dark-card': '#1a1828',
        'dark-border': '#334155',
      },
      fontFamily: {
        sans: ['Pretendard', 'Inter', '-apple-system', 'BlinkMacSystemFont', 'sans-serif'],
        display: ['Pretendard', 'Poppins', 'sans-serif'],
        mono: ['JetBrains Mono', 'Fira Code', 'monospace'],
      },
      boxShadow: {
        'glow-sm': '0 0 10px rgba(14, 165, 233, 0.3)',
        'glow': '0 0 20px rgba(14, 165, 233, 0.3)',
        'glow-lg': '0 0 30px rgba(14, 165, 233, 0.4)',
      },
      animation: {
        'fade-in': 'fadeIn 0.5s ease-in-out',
        'slide-up': 'slideUp 0.5s ease-in-out',
        'shimmer': 'shimmer 1.5s ease-in-out infinite',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { opacity: '0', transform: 'translateY(20px)' },
          '100%': { opacity: '1', transform: 'translateY(0)' },
        },
        shimmer: {
          '0%': { backgroundPosition: '200% 0' },
          '100%': { backgroundPosition: '-200% 0' },
        },
      },
    },
  },
  plugins: [],
}
```

---

## 접근성 고려사항

### 색상 대비
- WCAG AA 기준 최소 4.5:1 대비율 유지
- 중요한 정보는 색상만으로 전달하지 않음
- Dark mode에서도 충분한 대비 확보

### 키보드 네비게이션
- 모든 인터랙티브 요소에 포커스 스타일 제공
- Tab 순서가 논리적 흐름을 따름
- Skip navigation 링크 제공

### 스크린 리더
- 의미 있는 alt 텍스트
- ARIA 레이블 적절히 사용
- 시맨틱 HTML 사용

---

## 마무리

이 디자인 시스템은 "당신이 잠든 사이" 프로젝트에 **신뢰**, **희망**, **활력**을 불어넣습니다.

### 핵심 원칙
1. **긍정적인 첫인상**: 밝고 따뜻한 색상으로 사용자를 환영
2. **명확한 정보 전달**: 색상과 타이포그래피로 시각적 계층 구축
3. **부드러운 인터랙션**: 애니메이션과 전환 효과로 즐거운 경험
4. **일관된 경험**: Light/Dark 모드 모두에서 동일한 품질

매일 아침 사용자에게 **새로운 기회**와 **희망**을 전달하는 서비스답게, 디자인도 그 메시지를 담아야 합니다. 🌅✨

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/siyeolryu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
