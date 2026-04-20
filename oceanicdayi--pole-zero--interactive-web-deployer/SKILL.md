---
name: interactive-web-deployer
description: 創建高品質的互動式網頁應用程式，並自動佈署到 GitHub Pages。包含現代化前端技術、響應式設計、互動功能，以及完整的 CI/CD 流程。 Use when this capability is needed.
metadata:
  author: oceanicdayi
---

# 互動式網頁部署技能 (Interactive Web Deployer)

此 skill 用於創建專業級的互動式網頁應用程式，並自動化部署到 GitHub Pages。適用於資料視覺化、互動工具、文檔網站、專案展示等場景。

## 何時使用此 skill

- 需要創建具有互動功能的網頁應用程式
- 需要資料視覺化或圖表展示
- 需要建立專案的 demo 或展示網站
- 需要自動化部署到 GitHub Pages
- 需要響應式設計支援多種裝置
- 需要快速原型開發與迭代

## 核心原則

### 1. 零依賴優先 (Zero-Dependency First)

優先使用 CDN 載入的方式，避免需要 npm install 或複雜的建置流程：

✅ **推薦技術棧**：
- React (CDN)
- Vue (CDN)  
- Vanilla JavaScript
- Tailwind CSS (CDN)
- Chart.js / D3.js (CDN)

❌ **避免**：
- 需要 webpack/vite 建置的專案
- 複雜的依賴管理
- 需要編譯步驟的框架（除非必要）

### 2. 效能優化

- 使用 production 版本的函式庫
- 圖片優化與 lazy loading
- 程式碼分割與按需載入
- 最小化初始載入時間

### 3. 響應式設計

- Mobile-first 設計理念
- 支援桌面、平板、手機
- 觸控友善的互動元素
- 適當的斷點設計

### 4. 可訪問性 (Accessibility)

- 語義化 HTML
- ARIA 標籤
- 鍵盤導航支援
- 色彩對比度符合標準

---

## 工作流程

### 階段一：需求分析與規劃

#### 1. 確認專案需求

詢問或分析以下問題：
- **目的**：網頁的主要功能是什麼？
- **受眾**：目標使用者是誰？
- **資料**：需要展示什麼資料？
- **互動**：需要哪些互動功能？
- **設計風格**：偏好的視覺風格？

#### 2. 技術選型

根據需求選擇合適的技術：

| 需求類型 | 推薦技術 | 理由 |
|---------|---------|------|
| 資料視覺化 | React + D3.js/Chart.js | 強大的圖表能力 |
| 簡單展示頁 | Vanilla JS + Tailwind | 輕量快速 |
| 複雜互動 | React + Hooks | 狀態管理方便 |
| 表單處理 | Vue + Tailwind | 雙向綁定便利 |
| 3D 視覺化 | Three.js | 專業 3D 渲染 |

#### 3. 設計架構

規劃檔案結構：

```
project/
├── index.html          # 主頁面
├── assets/            
│   ├── css/           # 自訂樣式
│   ├── js/            # JavaScript 模組
│   └── images/        # 圖片資源
├── data/              # 資料檔案（JSON/CSV）
├── README.md          # 說明文件
└── .github/
    └── workflows/
        └── deploy.yml # GitHub Actions 部署設定
```

### 階段二：開發高品質網頁

#### 1. 建立基礎 HTML 結構

使用語義化 HTML5 標籤：

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="專案描述">
    <meta name="keywords" content="關鍵字">
    <meta property="og:title" content="網頁標題">
    <meta property="og:description" content="分享描述">
    <meta property="og:image" content="預覽圖片">
    <title>網頁標題</title>
    
    <!-- Favicon -->
    <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🚀</text></svg>">
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- React & ReactDOM (如需要) -->
    <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    
    <!-- Babel (用於 JSX) -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body>
    <div id="root"></div>
    <script type="text/babel" src="app.js"></script>
</body>
</html>
```

#### 2. 實作核心功能

**狀態管理**：
```javascript
const { useState, useEffect, useMemo } = React;

function App() {
    const [data, setData] = useState([]);
    const [loading, setLoading] = useState(true);
    const [activeTab, setActiveTab] = useState('overview');
    
    useEffect(() => {
        // 載入資料
        fetch('data/dataset.json')
            .then(res => res.json())
            .then(data => {
                setData(data);
                setLoading(false);
            });
    }, []);
    
    // ... 其他邏輯
}
```

**互動元素**：
```javascript
// 可拖曳元素
const handleDrag = (e) => {
    // 實作拖曳邏輯
};

// 響應式圖表
const ResponsiveChart = ({ data }) => {
    const containerRef = useRef(null);
    const [width, setWidth] = useState(0);
    
    useEffect(() => {
        const handleResize = () => {
            if (containerRef.current) {
                setWidth(containerRef.current.offsetWidth);
            }
        };
        
        handleResize();
        window.addEventListener('resize', handleResize);
        return () => window.removeEventListener('resize', handleResize);
    }, []);
    
    return <svg width={width} height={400}>...</svg>;
};
```

#### 3. 設計使用者介面

**色彩系統**：
```javascript
// Tailwind 自訂配置
tailwind.config = {
    theme: {
        extend: {
            colors: {
                primary: {
                    50: '#f0f9ff',
                    500: '#3b82f6',
                    900: '#1e3a8a'
                }
            }
        }
    }
};
```

**佈局模式**：
```html
<!-- 卡片式佈局 -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
    <div class="bg-white rounded-xl shadow-lg p-6">
        <!-- 卡片內容 -->
    </div>
</div>

<!-- 側邊欄佈局 -->
<div class="flex min-h-screen">
    <aside class="w-64 bg-slate-800 text-white p-6">
        <!-- 側邊欄 -->
    </aside>
    <main class="flex-1 p-8">
        <!-- 主內容 -->
    </main>
</div>
```

#### 4. 效能優化技巧

**圖片優化**：
```html
<!-- 響應式圖片 -->
<img 
    srcset="image-small.jpg 480w, image-large.jpg 1200w"
    sizes="(max-width: 600px) 480px, 1200px"
    src="image-large.jpg"
    alt="描述"
    loading="lazy"
>
```

**程式碼優化**：
```javascript
// 使用 useMemo 避免重複計算
const expensiveCalculation = useMemo(() => {
    return data.reduce((acc, item) => acc + item.value, 0);
}, [data]);

// 防抖處理
const debouncedSearch = useMemo(
    () => debounce((value) => performSearch(value), 300),
    []
);
```

#### 5. 加入動畫與過渡效果

```css
/* CSS 過渡 */
.card {
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card:hover {
    transform: translateY(-4px);
    box-shadow: 0 12px 24px rgba(0, 0, 0, 0.15);
}

/* 載入動畫 */
@keyframes spin {
    to { transform: rotate(360deg); }
}

.loading {
    animation: spin 1s linear infinite;
}
```

### 階段三：測試與優化

#### 1. 本地測試

```bash
# 使用 Python 啟動本地伺服器
python3 -m http.server 8000

# 或使用 Node.js
npx http-server -p 8000
```

**測試清單**：
- [ ] 在 Chrome、Firefox、Safari 中測試
- [ ] 測試手機與平板裝置
- [ ] 檢查所有互動功能
- [ ] 驗證資料載入
- [ ] 測試錯誤處理
- [ ] 檢查載入速度

#### 2. 效能檢測

使用 Chrome DevTools Lighthouse：
- Performance: 目標 > 90
- Accessibility: 目標 > 90
- Best Practices: 目標 > 90
- SEO: 目標 > 90

#### 3. 程式碼優化

```javascript
// 錯誤處理
async function fetchData() {
    try {
        const response = await fetch('data.json');
        if (!response.ok) throw new Error('Failed to fetch');
        return await response.json();
    } catch (error) {
        console.error('Error:', error);
        // 顯示錯誤訊息給使用者
    }
}

// 載入狀態
if (loading) {
    return <div className="flex items-center justify-center h-screen">
        <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-blue-500"></div>
    </div>;
}
```

### 階段四：部署到 GitHub Pages

#### 1. 建立 GitHub Actions 工作流程

創建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup Pages
        uses: actions/configure-pages@v4
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'
      
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

#### 2. 配置 GitHub Repository

在 repository 設定中：
1. 進入 **Settings** > **Pages**
2. Source 選擇 **GitHub Actions**
3. 儲存設定

#### 3. 觸發部署

```bash
# 提交變更
git add .
git commit -m "Add interactive web application"
git push origin main

# GitHub Actions 會自動執行部署
```

#### 4. 驗證部署

部署完成後，網站將可在以下網址訪問：
```
https://<username>.github.io/<repository-name>/
```

#### 5. 自訂域名（選用）

如果有自訂域名：

1. 在 repository 根目錄創建 `CNAME` 檔案：
```
www.yourdomain.com
```

2. 在域名提供商設定 DNS：
```
Type: CNAME
Name: www
Value: <username>.github.io
```

### 階段五：維護與迭代

#### 1. 監控與分析

加入 Google Analytics（選用）：
```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

#### 2. 持續優化

- 收集使用者回饋
- 監控載入時間
- 優化圖片與資源
- 更新內容與功能

#### 3. 版本管理

使用 Git tags 標記重要版本：
```bash
git tag -a v1.0.0 -m "Initial release"
git push origin v1.0.0
```

---

## 最佳實踐範例

### 範例一：資料視覺化儀表板

```javascript
function Dashboard() {
    const [data, setData] = useState([]);
    const [filter, setFilter] = useState('all');
    
    const filteredData = useMemo(() => {
        if (filter === 'all') return data;
        return data.filter(item => item.category === filter);
    }, [data, filter]);
    
    return (
        <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100">
            <header className="bg-white shadow-sm">
                <div className="container mx-auto px-4 py-6">
                    <h1 className="text-3xl font-bold text-gray-900">
                        資料儀表板
                    </h1>
                </div>
            </header>
            
            <main className="container mx-auto px-4 py-8">
                <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                    <StatCard title="總數" value={data.length} icon="📊" />
                    <StatCard title="平均值" value={calculateAverage(data)} icon="📈" />
                    <StatCard title="成長率" value="+12%" icon="🚀" />
                </div>
                
                <div className="bg-white rounded-xl shadow-lg p-6">
                    <ChartComponent data={filteredData} />
                </div>
            </main>
        </div>
    );
}
```

### 範例二：互動式文檔網站

```javascript
function Documentation() {
    const [activeSection, setActiveSection] = useState('intro');
    const [searchTerm, setSearchTerm] = useState('');
    
    return (
        <div className="flex min-h-screen">
            <Sidebar 
                sections={sections}
                activeSection={activeSection}
                onSectionChange={setActiveSection}
            />
            
            <main className="flex-1 p-8">
                <SearchBar value={searchTerm} onChange={setSearchTerm} />
                <ContentArea section={activeSection} searchTerm={searchTerm} />
                <NavigationButtons 
                    onPrev={() => goToPrevSection()}
                    onNext={() => goToNextSection()}
                />
            </main>
        </div>
    );
}
```

### 範例三：互動式工具

```javascript
function InteractiveTool() {
    const [input, setInput] = useState('');
    const [result, setResult] = useState(null);
    
    const handleCalculate = () => {
        // 執行計算或處理
        const processed = processInput(input);
        setResult(processed);
    };
    
    return (
        <div className="max-w-4xl mx-auto p-8">
            <div className="bg-white rounded-2xl shadow-xl p-8">
                <h2 className="text-2xl font-bold mb-6">互動式計算工具</h2>
                
                <div className="space-y-4">
                    <input
                        type="text"
                        value={input}
                        onChange={(e) => setInput(e.target.value)}
                        className="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500"
                        placeholder="輸入資料..."
                    />
                    
                    <button
                        onClick={handleCalculate}
                        className="w-full bg-blue-500 text-white px-6 py-3 rounded-lg hover:bg-blue-600 transition-colors"
                    >
                        計算
                    </button>
                </div>
                
                {result && (
                    <div className="mt-6 p-4 bg-green-50 rounded-lg">
                        <ResultDisplay data={result} />
                    </div>
                )}
            </div>
        </div>
    );
}
```

---

## 常見問題排除

### 問題 1：GitHub Pages 顯示 404

**解決方案**：
- 確認 repository 設定中 Pages 已啟用
- 檢查檔案路徑是否正確（區分大小寫）
- 確認 index.html 在正確位置

### 問題 2：CSS/JS 載入失敗

**解決方案**：
- 使用相對路徑而非絕對路徑
- 檢查 CDN 連結是否有效
- 確認網路連線正常

### 問題 3：在手機上顯示異常

**解決方案**：
- 確認有 viewport meta 標籤
- 使用響應式單位（rem、%、vw/vh）
- 測試不同斷點
- 確保觸控目標夠大（至少 44x44px）

### 問題 4：載入速度慢

**解決方案**：
- 優化圖片大小
- 使用 lazy loading
- 移除未使用的程式碼
- 使用 CDN 的 production 版本
- 啟用瀏覽器快取

---

## 進階技巧

### 1. PWA（漸進式網頁應用）

添加 Service Worker 支援離線使用：

```javascript
// sw.js
self.addEventListener('install', (event) => {
    event.waitUntil(
        caches.open('v1').then((cache) => {
            return cache.addAll([
                '/',
                '/index.html',
                '/styles.css',
                '/app.js'
            ]);
        })
    );
});
```

### 2. 深色模式支援

```javascript
function ThemeToggle() {
    const [isDark, setIsDark] = useState(false);
    
    useEffect(() => {
        document.documentElement.classList.toggle('dark', isDark);
    }, [isDark]);
    
    return (
        <button onClick={() => setIsDark(!isDark)}>
            {isDark ? '🌞' : '🌙'}
        </button>
    );
}
```

### 3. 國際化支援

```javascript
const translations = {
    'zh-TW': { title: '標題', description: '描述' },
    'en': { title: 'Title', description: 'Description' }
};

function useTranslation(lang) {
    return translations[lang] || translations['zh-TW'];
}
```

---

## 品質檢查清單

在完成部署前，確認以下項目：

### 功能性
- [ ] 所有互動功能正常運作
- [ ] 資料正確載入與顯示
- [ ] 錯誤處理完善
- [ ] 表單驗證正確

### 使用者體驗
- [ ] 載入時間 < 3 秒
- [ ] 響應式設計在所有裝置正常
- [ ] 動畫流暢（60fps）
- [ ] 無水平滾動條
- [ ] 觸控友善

### 技術品質
- [ ] HTML 驗證通過
- [ ] Console 無錯誤訊息
- [ ] Lighthouse 分數 > 90
- [ ] SEO meta 標籤完整
- [ ] 可訪問性符合 WCAG 2.1

### 內容品質
- [ ] 文字內容無錯別字
- [ ] 圖片有 alt 屬性
- [ ] 連結都可正常訪問
- [ ] README 文件完整

### 部署
- [ ] GitHub Actions 成功執行
- [ ] 網站可正常訪問
- [ ] 自訂域名設定正確（如有）
- [ ] HTTPS 啟用

---

## 參考資源

### 官方文檔
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [React Documentation](https://react.dev/)
- [Tailwind CSS](https://tailwindcss.com/)
- [MDN Web Docs](https://developer.mozilla.org/)

### 工具與資源
- [Lighthouse](https://developers.google.com/web/tools/lighthouse): 效能檢測
- [Can I Use](https://caniuse.com/): 瀏覽器相容性
- [Unsplash](https://unsplash.com/): 免費高品質圖片
- [Google Fonts](https://fonts.google.com/): 網頁字型

### 學習資源
- [Web.dev](https://web.dev/): Google 的網頁開發指南
- [JavaScript.info](https://javascript.info/): 現代 JavaScript 教學
- [CSS-Tricks](https://css-tricks.com/): CSS 技巧與教學

---

## 總結

此 skill 提供完整的工作流程，從需求分析到部署維護，確保創建出高品質的互動式網頁應用程式。重點在於：

1. ✅ **簡單快速**：使用 CDN，無需複雜建置
2. ✅ **專業品質**：現代化設計與效能優化
3. ✅ **自動部署**：GitHub Actions 一鍵部署
4. ✅ **持續改進**：監控與迭代優化

遵循此 skill 的指引，可以快速創建並部署專業級的網頁應用程式。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oceanicdayi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
