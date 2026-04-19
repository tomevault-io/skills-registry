---
name: ai-adaptive-uxui-design
description: 支援 AI 彈性化調整的 UX/UI 設計指南 - 自適應介面、串流回應、載入狀態、對話式 UI Use when this capability is needed.
metadata:
  author: hankhuang0516
---

# AI Adaptive UX/UI Design Guide

本 Skill 提供支援 AI 功能的彈性化 UX/UI 設計指南，涵蓋自適應介面、生成式 UI、串流回應顯示、載入狀態、對話式介面及深色模式等最佳實踐。

---

## 1. AI 驅動的 UX 設計模式

### 五大核心設計模式

| 模式 | 說明 | 應用場景 |
|------|------|----------|
| **Predictive UX** | AI 預測使用者意圖，自動完成操作 | 搜尋自動完成、智慧回覆建議 |
| **Generative Assistance** | AI 根據提示生成或共創內容 | 文字生成、圖片創作、程式碼輔助 |
| **Adaptive Personalization** | AI 持續學習，為每位使用者客製化介面 | 動態內容推薦、個人化佈局 |
| **Conversational Interface** | 透過自然對話完成複雜工作流程 | Chatbot、語音助理 |
| **Background Automation** | AI 在背景處理長時間任務，完成後通知 | 批次處理、資料分析 |

### 設計原則

```
⚠️ 重要提醒：
- 更多 AI ≠ 更好的 UX
- 過度自動化會降低使用者控制感
- 找到「最佳平衡點」是關鍵
```

---

## 2. The Shape of AI - UX 模式分類

### 六大模式類別

#### 1. Wayfinders (引導者)
幫助使用者開始與 AI 互動：

| 模式 | 說明 |
|------|------|
| **Gallery** | 展示範例生成結果 |
| **Templates** | 預設提示詞模板 |
| **Suggestions** | 智慧建議 |
| **Nudges** | 輕推提示 |
| **Randomize** | 隨機生成選項 |

#### 2. Prompt Actions (輸入動作)
使用者與 AI 的直接互動：

| 模式 | 說明 |
|------|------|
| **Open Input** | 自由文字輸入 |
| **Auto-fill** | 自動填充 |
| **Expand** | 擴展內容 |
| **Summarize** | 摘要生成 |
| **Transform** | 內容轉換 |
| **Regenerate** | 重新生成 |

#### 3. Tuners (調整器)
精細調整控制：

| 模式 | 說明 |
|------|------|
| **Parameters** | 參數調整 (溫度、長度) |
| **Modes** | 模式切換 (創意/精確) |
| **Voice & Tone** | 語調設定 |
| **Preset Styles** | 預設風格 |
| **Attachments** | 附件上傳 |

#### 4. Governors (監督者)
人類在迴圈中的控制：

| 模式 | 說明 |
|------|------|
| **Action Plans** | 執行計畫預覽 |
| **Draft Mode** | 草稿模式 |
| **Variations** | 多版本選擇 |
| **Citations** | 引用來源 |
| **Stream of Thought** | 思考過程展示 |

#### 5. Trust Builders (信任建立者)
倫理與透明度保障：

| 模式 | 說明 |
|------|------|
| **AI Disclosure** | AI 身份揭露 |
| **Caveats** | 免責聲明 |
| **Consent** | 同意機制 |
| **Data Ownership** | 資料所有權控制 |
| **Watermark** | 浮水印標記 |

#### 6. Identifiers (識別標記)
品牌層級的 AI 區分：

| 模式 | 說明 |
|------|------|
| **Avatar** | AI 頭像 |
| **Color Coding** | 顏色區分 |
| **Naming** | 命名 (如 "Copilot") |
| **Personality** | 個性特徵 |

---

## 3. 串流回應 UI (Streaming Response)

### 為什麼需要串流？

```
傳統阻塞式 UI:
使用者輸入 → 等待 5-40 秒 → 一次顯示完整回應 ❌

串流式 UI:
使用者輸入 → 即時逐字顯示 → 持續更新直到完成 ✅
```

### 打字機效果 (Typewriter Effect)

```typescript
// 平滑串流文字動畫
const CHAR_DELAY = 5; // 5ms/字元 = 200 字元/秒

const StreamingText = ({ content }: { content: string }) => {
    const [displayedText, setDisplayedText] = useState('');
    const [currentIndex, setCurrentIndex] = useState(0);

    useEffect(() => {
        if (currentIndex < content.length) {
            const timer = setTimeout(() => {
                setDisplayedText(prev => prev + content[currentIndex]);
                setCurrentIndex(prev => prev + 1);
            }, CHAR_DELAY);
            return () => clearTimeout(timer);
        }
    }, [currentIndex, content]);

    return <div className="whitespace-pre-wrap">{displayedText}</div>;
};
```

### Vercel AI SDK 整合

```typescript
import { useChat } from 'ai/react';

function ChatComponent() {
    const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat();

    return (
        <div className="flex flex-col h-full">
            {/* 訊息列表 */}
            <div className="flex-1 overflow-y-auto">
                {messages.map((message) => (
                    <div
                        key={message.id}
                        className={`p-4 ${
                            message.role === 'user'
                                ? 'bg-blue-100 ml-auto'
                                : 'bg-gray-100'
                        }`}
                    >
                        {message.content}
                    </div>
                ))}
            </div>

            {/* 輸入區域 */}
            <form onSubmit={handleSubmit} className="p-4 border-t">
                <input
                    value={input}
                    onChange={handleInputChange}
                    disabled={isLoading}
                    placeholder="輸入訊息..."
                    className="w-full p-2 border rounded"
                />
            </form>
        </div>
    );
}
```

### 串流速度建議

| 場景 | 延遲 | 說明 |
|------|------|------|
| 快速回應 | 5ms/字元 | 適合短回覆 |
| 自然閱讀 | 80-120ms/字元 | 模擬人類打字 |
| 有個性的 AI | 變化速度 | 增加擬人感 |

---

## 4. 載入狀態設計

### AI 回應的兩個階段

```
1. Processing (處理中)
   └── AI 接收並理解請求，尚無輸出

2. Generation (生成中)
   └── AI 開始產生回應，可串流顯示
```

### Skeleton Loading (骨架載入)

```tsx
// 基本骨架元件
const Skeleton = ({ className }: { className?: string }) => (
    <div
        className={`animate-pulse bg-gray-200 rounded ${className}`}
    />
);

// AI 回應骨架
const AIResponseSkeleton = () => (
    <div className="space-y-3 p-4">
        <Skeleton className="h-4 w-3/4" />
        <Skeleton className="h-4 w-full" />
        <Skeleton className="h-4 w-5/6" />
        <Skeleton className="h-4 w-2/3" />
    </div>
);
```

### Shimmer 效果 (閃爍動畫)

```css
/* Shimmer 動畫 */
@keyframes shimmer {
    0% {
        background-position: -200% 0;
    }
    100% {
        background-position: 200% 0;
    }
}

.shimmer {
    background: linear-gradient(
        90deg,
        #f0f0f0 25%,
        #e0e0e0 50%,
        #f0f0f0 75%
    );
    background-size: 200% 100%;
    animation: shimmer 1.5s infinite;
}
```

```tsx
// React Shimmer 元件
const ShimmerSkeleton = ({ className }: { className?: string }) => (
    <div
        className={`
            bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200
            bg-[length:200%_100%]
            animate-[shimmer_1.5s_infinite]
            rounded
            ${className}
        `}
    />
);
```

### Typing Indicator (打字指示器)

```tsx
// 三點跳動動畫
const TypingIndicator = () => (
    <div className="flex items-center space-x-1 p-3 bg-gray-100 rounded-lg w-fit">
        <span className="w-2 h-2 bg-gray-400 rounded-full animate-bounce"
              style={{ animationDelay: '0ms' }} />
        <span className="w-2 h-2 bg-gray-400 rounded-full animate-bounce"
              style={{ animationDelay: '150ms' }} />
        <span className="w-2 h-2 bg-gray-400 rounded-full animate-bounce"
              style={{ animationDelay: '300ms' }} />
    </div>
);

// 帶文字的指示器
const TypingIndicatorWithText = ({ name = 'AI' }: { name?: string }) => (
    <div className="flex items-center space-x-2 text-gray-500 text-sm">
        <TypingIndicator />
        <span>{name} is typing...</span>
    </div>
);
```

### 載入狀態最佳實踐

| 做法 | 說明 |
|------|------|
| ✅ 小於 1 秒不顯示載入 | 避免閃爍 |
| ✅ 支援串流就直接顯示 | 跳過處理階段的載入 |
| ✅ 骨架要符合實際佈局 | 給使用者正確預期 |
| ✅ 提供取消選項 | 長時間處理時可中斷 |
| ❌ 不要只顯示空白框架 | 要有內容預覽形狀 |
| ❌ 避免過度動畫 | 可能造成無障礙問題 |

---

## 5. 對話式 UI (Conversational Interface)

### 訊息氣泡設計

```tsx
interface MessageProps {
    role: 'user' | 'assistant';
    content: string;
    timestamp?: Date;
}

const MessageBubble = ({ role, content, timestamp }: MessageProps) => {
    const isUser = role === 'user';

    return (
        <div className={`flex ${isUser ? 'justify-end' : 'justify-start'} mb-4`}>
            {/* AI 頭像 */}
            {!isUser && (
                <div className="w-8 h-8 rounded-full bg-purple-500 flex items-center justify-center mr-2">
                    <span className="text-white text-sm">AI</span>
                </div>
            )}

            <div className={`max-w-[70%] ${isUser ? 'order-1' : ''}`}>
                <div
                    className={`
                        px-4 py-2 rounded-2xl
                        ${isUser
                            ? 'bg-blue-500 text-white rounded-br-md'
                            : 'bg-gray-100 text-gray-900 rounded-bl-md'
                        }
                    `}
                >
                    {content}
                </div>

                {timestamp && (
                    <span className="text-xs text-gray-400 mt-1 block">
                        {timestamp.toLocaleTimeString()}
                    </span>
                )}
            </div>
        </div>
    );
};
```

### 快速回覆按鈕 (Quick Replies)

```tsx
interface QuickReply {
    label: string;
    value: string;
}

const QuickReplies = ({
    options,
    onSelect
}: {
    options: QuickReply[];
    onSelect: (value: string) => void;
}) => (
    <div className="flex flex-wrap gap-2 mt-3">
        {options.map((option) => (
            <button
                key={option.value}
                onClick={() => onSelect(option.value)}
                className="
                    px-4 py-2
                    border border-blue-500
                    text-blue-500
                    rounded-full
                    hover:bg-blue-50
                    transition-colors
                    text-sm
                "
            >
                {option.label}
            </button>
        ))}
    </div>
);

// 使用範例
<QuickReplies
    options={[
        { label: '查看訂單', value: 'view_orders' },
        { label: '聯繫客服', value: 'contact_support' },
        { label: '常見問題', value: 'faq' }
    ]}
    onSelect={handleQuickReply}
/>
```

### 對話流程設計原則

| 原則 | 說明 |
|------|------|
| **首次互動** | 5 秒內決定是否繼續，避免長歡迎訊息 |
| **減少摩擦** | 短提示、限制選項數、提供快速回覆 |
| **透明度** | 告知使用者正在與 AI 對話 |
| **錯誤處理** | 優雅降級，提供人工客服選項 |
| **上下文記憶** | 記住對話歷史，避免重複詢問 |

---

## 6. 自適應介面 (Adaptive Interface)

### 根據使用者層級調整

```tsx
type UserLevel = 'beginner' | 'intermediate' | 'expert';

const AdaptiveToolbar = ({ userLevel }: { userLevel: UserLevel }) => {
    const tools = {
        beginner: ['undo', 'redo', 'save'],
        intermediate: ['undo', 'redo', 'save', 'export', 'share'],
        expert: ['undo', 'redo', 'save', 'export', 'share', 'settings', 'plugins', 'api']
    };

    return (
        <div className="flex gap-2 p-2 bg-gray-100 rounded">
            {tools[userLevel].map((tool) => (
                <button key={tool} className="p-2 hover:bg-gray-200 rounded">
                    {tool}
                </button>
            ))}
        </div>
    );
};
```

### 根據時間/情境調整

```tsx
const useAdaptiveTheme = () => {
    const [theme, setTheme] = useState<'light' | 'dark'>('light');

    useEffect(() => {
        const hour = new Date().getHours();
        // 晚上 7 點到早上 7 點自動切換深色模式
        const shouldBeDark = hour >= 19 || hour < 7;

        // 也可以偵測系統設定
        const systemPrefersDark = window.matchMedia(
            '(prefers-color-scheme: dark)'
        ).matches;

        setTheme(shouldBeDark || systemPrefersDark ? 'dark' : 'light');
    }, []);

    return theme;
};
```

### Generative UI (生成式介面)

```tsx
// AI 根據使用者需求動態生成介面
const GenerativeUI = ({ userIntent }: { userIntent: string }) => {
    const [uiComponents, setUiComponents] = useState<React.ReactNode[]>([]);

    useEffect(() => {
        // 根據意圖生成對應的 UI 元件
        switch (userIntent) {
            case 'view_summary':
                setUiComponents([
                    <SummaryCard key="summary" />,
                    <QuickActions key="actions" />
                ]);
                break;
            case 'detailed_analysis':
                setUiComponents([
                    <DataTable key="table" />,
                    <Charts key="charts" />,
                    <ExportOptions key="export" />
                ]);
                break;
            default:
                setUiComponents([<DefaultView key="default" />]);
        }
    }, [userIntent]);

    return <div className="space-y-4">{uiComponents}</div>;
};
```

---

## 7. 深色/淺色模式

### 主題系統實作

```tsx
// ThemeContext.tsx
import { createContext, useContext, useState, useEffect } from 'react';

type Theme = 'light' | 'dark' | 'system';

interface ThemeContextType {
    theme: Theme;
    actualTheme: 'light' | 'dark';
    setTheme: (theme: Theme) => void;
}

const ThemeContext = createContext<ThemeContextType | null>(null);

export const ThemeProvider = ({ children }: { children: React.ReactNode }) => {
    const [theme, setTheme] = useState<Theme>(() => {
        return (localStorage.getItem('theme') as Theme) || 'system';
    });

    const [actualTheme, setActualTheme] = useState<'light' | 'dark'>('light');

    useEffect(() => {
        const updateActualTheme = () => {
            if (theme === 'system') {
                const systemDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
                setActualTheme(systemDark ? 'dark' : 'light');
            } else {
                setActualTheme(theme);
            }
        };

        updateActualTheme();

        // 監聽系統主題變化
        const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
        mediaQuery.addEventListener('change', updateActualTheme);

        return () => mediaQuery.removeEventListener('change', updateActualTheme);
    }, [theme]);

    useEffect(() => {
        localStorage.setItem('theme', theme);
        document.documentElement.classList.toggle('dark', actualTheme === 'dark');
    }, [theme, actualTheme]);

    return (
        <ThemeContext.Provider value={{ theme, actualTheme, setTheme }}>
            {children}
        </ThemeContext.Provider>
    );
};

export const useTheme = () => {
    const context = useContext(ThemeContext);
    if (!context) throw new Error('useTheme must be used within ThemeProvider');
    return context;
};
```

### 主題切換元件

```tsx
import { Sun, Moon, Monitor } from 'lucide-react';

const ThemeToggle = () => {
    const { theme, setTheme } = useTheme();

    const options = [
        { value: 'light', icon: Sun, label: '淺色' },
        { value: 'dark', icon: Moon, label: '深色' },
        { value: 'system', icon: Monitor, label: '系統' }
    ] as const;

    return (
        <div className="flex bg-gray-100 dark:bg-gray-800 rounded-lg p-1">
            {options.map(({ value, icon: Icon, label }) => (
                <button
                    key={value}
                    onClick={() => setTheme(value)}
                    className={`
                        flex items-center gap-2 px-3 py-2 rounded-md text-sm
                        transition-colors
                        ${theme === value
                            ? 'bg-white dark:bg-gray-700 shadow'
                            : 'hover:bg-gray-200 dark:hover:bg-gray-700'
                        }
                    `}
                    aria-label={label}
                >
                    <Icon className="w-4 h-4" />
                    <span className="hidden sm:inline">{label}</span>
                </button>
            ))}
        </div>
    );
};
```

### Tailwind CSS 深色模式配置

```javascript
// tailwind.config.js
module.exports = {
    darkMode: 'class', // 或 'media' 自動跟隨系統
    theme: {
        extend: {
            colors: {
                // 深色模式專用色彩
                dark: {
                    bg: '#121212',      // 避免純黑
                    surface: '#1E1E1E',
                    elevated: '#2D2D2D',
                    border: '#3D3D3D'
                }
            }
        }
    }
}
```

### 深色模式色彩最佳實踐

| 類別 | 淺色模式 | 深色模式 | 說明 |
|------|----------|----------|------|
| 背景 | `#FFFFFF` | `#121212` | 避免純黑 `#000000` |
| 表面 | `#F5F5F5` | `#1E1E1E` | 卡片、對話框 |
| 提升 | `#EEEEEE` | `#2D2D2D` | 高層級元素 |
| 文字 | `#212121` | `#E0E0E0` | 避免純白 `#FFFFFF` |
| 次要文字 | `#757575` | `#9E9E9E` | 輔助說明 |
| 強調色 | 飽和色 | 降低飽和度 | 減少眼睛疲勞 |

---

## 8. 無障礙設計 (Accessibility)

### AI 介面的無障礙考量

```tsx
// 支援螢幕閱讀器的 AI 回應
const AccessibleAIResponse = ({
    content,
    isLoading
}: {
    content: string;
    isLoading: boolean;
}) => (
    <div
        role="region"
        aria-label="AI 回應"
        aria-live="polite"
        aria-busy={isLoading}
    >
        {isLoading ? (
            <span aria-label="AI 正在思考中">
                <TypingIndicator />
            </span>
        ) : (
            <div>{content}</div>
        )}
    </div>
);
```

### 減少動畫偏好

```tsx
// 尊重使用者的動畫偏好設定
const useReducedMotion = () => {
    const [prefersReducedMotion, setPrefersReducedMotion] = useState(false);

    useEffect(() => {
        const mediaQuery = window.matchMedia('(prefers-reduced-motion: reduce)');
        setPrefersReducedMotion(mediaQuery.matches);

        const handler = (e: MediaQueryListEvent) => setPrefersReducedMotion(e.matches);
        mediaQuery.addEventListener('change', handler);
        return () => mediaQuery.removeEventListener('change', handler);
    }, []);

    return prefersReducedMotion;
};

// 使用
const AnimatedComponent = () => {
    const reducedMotion = useReducedMotion();

    return (
        <div
            className={reducedMotion ? '' : 'animate-bounce'}
        >
            {/* 內容 */}
        </div>
    );
};
```

### 無障礙檢查清單

| 項目 | 說明 |
|------|------|
| ✅ 鍵盤導航 | 所有互動元素可用鍵盤操作 |
| ✅ 螢幕閱讀器 | `aria-live` 宣告 AI 回應更新 |
| ✅ 色彩對比 | WCAG 2.1 AA 標準 (4.5:1) |
| ✅ 減少動畫選項 | 尊重 `prefers-reduced-motion` |
| ✅ 文字大小 | 支援放大到 200% |
| ✅ 錯誤提示 | 清楚描述錯誤和解決方式 |

---

## 9. 錯誤處理 UI

### AI 錯誤狀態元件

```tsx
interface AIErrorProps {
    type: 'timeout' | 'rate_limit' | 'server_error' | 'content_filter';
    onRetry?: () => void;
}

const AIErrorMessage = ({ type, onRetry }: AIErrorProps) => {
    const errorConfig = {
        timeout: {
            icon: '⏱️',
            title: '回應逾時',
            message: 'AI 回應時間過長，請稍後再試',
            canRetry: true
        },
        rate_limit: {
            icon: '🚫',
            title: '請求過於頻繁',
            message: '請稍等一下再發送新的請求',
            canRetry: false
        },
        server_error: {
            icon: '⚠️',
            title: '服務暫時無法使用',
            message: '我們正在處理這個問題，請稍後再試',
            canRetry: true
        },
        content_filter: {
            icon: '🛡️',
            title: '內容無法處理',
            message: '您的請求包含無法處理的內容，請修改後重試',
            canRetry: false
        }
    };

    const config = errorConfig[type];

    return (
        <div className="bg-red-50 border border-red-200 rounded-lg p-4">
            <div className="flex items-start gap-3">
                <span className="text-2xl">{config.icon}</span>
                <div className="flex-1">
                    <h4 className="font-medium text-red-800">{config.title}</h4>
                    <p className="text-red-600 text-sm mt-1">{config.message}</p>

                    {config.canRetry && onRetry && (
                        <button
                            onClick={onRetry}
                            className="mt-3 px-4 py-2 bg-red-100 hover:bg-red-200
                                       text-red-700 rounded-md text-sm transition-colors"
                        >
                            重試
                        </button>
                    )}
                </div>
            </div>
        </div>
    );
};
```

---

## 10. 本專案實作參考

### AI 功能相關檔案

| 檔案 | 說明 |
|------|------|
| `client/src/pages/ApiDocsPage.tsx` | API 文件頁面 |
| `server/src/controllers/aiController.ts` | AI 控制器 |
| `server/src/routes/aiRoutes.ts` | AI 路由 |

### 現有 UI 元件

| 檔案 | 說明 |
|------|------|
| `client/src/components/ui/Dialog.tsx` | 對話框元件 |
| `client/src/components/ui/Button.tsx` | 按鈕元件 |
| `client/src/components/ui/Tabs.tsx` | 標籤頁元件 |

### 建議新增元件

```
client/src/components/ai/
├── StreamingText.tsx      # 串流文字顯示
├── TypingIndicator.tsx    # 打字指示器
├── ChatBubble.tsx         # 對話氣泡
├── QuickReplies.tsx       # 快速回覆按鈕
├── AIResponseSkeleton.tsx # AI 回應骨架
└── ThemeToggle.tsx        # 主題切換
```

---

## 11. 參考資源

### 設計模式
- [The Shape of AI - UX Patterns](https://www.shapeof.ai/)
- [AI-Driven UX Design Patterns - LogRocket](https://blog.logrocket.com/ux-design/ai-driven-ux-design-patterns/)
- [AI UI Patterns - Patterns.dev](https://www.patterns.dev/react/ai-ui-patterns/)

### 趨勢與指南
- [UX Trends 2026: AI, Zero UI, Adaptive Design](https://bitskingdom.com/blog/ux-trends-2026-ai-zero-ui-adaptive-design/)
- [Future of UI UX Design 2026](https://motiongility.com/future-of-ui-ux-design/)
- [10 AI-Driven UX Patterns - Orbix](https://www.orbix.studio/blogs/ai-driven-ux-patterns-saas-2026)

### 對話式 UI
- [Chatbot UI Examples - Eleken](https://www.eleken.co/blog-posts/chatbot-ui-examples)
- [Chatbot UI Design - Sendbird](https://sendbird.com/blog/chatbot-ui)
- [AI Chatbot UX Best Practices](https://www.letsgroto.com/blog/ux-best-practices-for-ai-chatbots)

### 串流與元件
- [Vercel AI SDK - Streaming](https://ai-sdk.dev/docs/foundations/streaming)
- [shadcn AI Components](https://www.shadcn.io/ai)
- [Smooth Text Streaming - Upstash](https://upstash.com/blog/smooth-streaming)

### 深色模式
- [Dark Mode UI Best Practices 2026](https://www.designstudiouiux.com/blog/dark-mode-ui-design-best-practices/)
- [Dark Mode UI Design - LogRocket](https://blog.logrocket.com/ux-design/dark-mode-ui-design-best-practices-and-examples/)

### 載入狀態
- [Skeleton Screens 101 - NN/g](https://www.nngroup.com/articles/skeleton-screens/)
- [GenAI Loading States - Cloudscape](https://cloudscape.design/patterns/genai/genai-loading-states/)

---

**Last Updated**: 2026-01-19
**Maintainer**: Wishlist App Team

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hankhuang0516) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
