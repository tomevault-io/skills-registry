---
name: map-display-rules
description: > Use when this capability is needed.
metadata:
  author: losangeles1156
---

# LUTAGU 地圖節點顯示規則 (Map Display Rules)

## 📋 目的

規範前端地圖節點的顯示邏輯，確保：
1. **五層級節點分層顯示** - 依重要性與 Zoom 層級控制可見性
2. **Hub 聚合機制** - 大型樞紐自動聚合鄰近車站
3. **鐵道公司配色** - 使用官方品牌色顯示節點
4. **效能最佳化** - Viewport 虛擬化渲染

---

## 🏗️ 五層級節點顯示系統 (5-Tier Display System)

### **Tier 1: 超級樞紐 (Super Hub)**
- **顯示條件**: **任何 Zoom 層級永遠顯示名稱** (包括關東全區視圖)
- **定義**: 東京核心交通樞紐 + 國際機場
- **節點列表** (共 10 個):
  ```
  東京站、上野、池袋、新宿、澀谷、品川、銀座、秋葉原
  成田機場 (NRT)、羽田機場 (HND)
  ```
- **資料庫標記**: `display_tier = 1`, `min_zoom_level = 1`
- **預設狀態**: **啟用 Hub 聚合模式**

### **Tier 2: 主要樞紐 (Major Hub)**
- **顯示條件**: `Zoom >= 12` 顯示名稱
- **定義**: 多線共構或車站聚集群
- **節點範例**:
  ```
  大手町、淺草、押上、新橋、東日本橋、濱松町、御徒町、
  御茶之水、飯田橋、中目黑、日暮里、泉岳寺、大井町、蒲田等
  ```
- **資料庫標記**: `display_tier = 2`, `min_zoom_level = 12`
- **預設狀態**: **啟用 Hub 聚合模式**

### **Tier 3: 次要樞紐 (Minor Hub)**
- **顯示條件**: `Zoom >= 14` 顯示名稱
- **定義**: 有共構或直通運轉的車站
- **資料庫標記**: `display_tier = 3`, `min_zoom_level = 14`

### **Tier 4: 常用車站 (Regular Station)**
- **顯示條件**: `Zoom >= 15` 顯示名稱
- **定義**: 依每日使用人次權重排序的車站
- **排序依據**: `daily_passengers` 欄位 (降序)
- **資料庫標記**: `display_tier = 4`, `min_zoom_level = 15`

### **Tier 5: 一般車站 (Local Station)**
- **顯示條件**: `Zoom >= 16` 顯示名稱
- **定義**: 單一車站且使用人數較少
- **資料庫標記**: `display_tier = 5`, `min_zoom_level = 16`

---

## 🎨 節點視覺規範

### **Hub 聚合節點 (Aggregated Hub)**

#### 名稱顯示
- **規則**: **僅顯示地名，不含鐵道公司或路線資訊**
- **範例**:
  - ✅ 正確: `上野`
  - ❌ 錯誤: `JR 上野站`、`上野駅 (JR/東京メトロ)`

#### 顏色標準
- **規則**: 採用**主要經營鐵道公司的官方品牌色**
- **主要鐵道公司配色表**:

| 鐵道公司 | 品牌色 (HEX) | 適用範例 |
|---------|-------------|---------|
| **JR 東日本** | `#00AC4E` (綠色) | 上野、東京、池袋、新宿、澀谷、品川、秋葉原 |
| **東京 Metro** | `#149BDF` (藍綠色) | 銀座、大手町、日本橋 |
| **都營地下鐵** | `#70BE1B` (淺綠色) | 新橋、押上 (依循用戶指定代表色) |
| **京急電鐵** | `#E31E24` (紅色) | 泉岳寺、品川 (京急優先時) |
| **東武鐵道** | `#003DA5` (深藍色) | 淺草、押上 (東武優先時) |
| **京成電鐵** | `#003DA5` (深藍色) | 成田機場、日暮里 |
| **東京單軌電車** | `#0071BC` (藍色) | 羽田機場 |

#### 判斷主要經營公司規則
1. **單一公司經營** → 使用該公司品牌色
2. **多公司共構** → 依以下優先順序:
   ```
   JR 東日本 > 東京 Metro > 都營地下鐵 > 私鐵 (京急/東武/京成等)
   ```
3. **特殊案例**:
   - 成田機場 → 京成電鐵藍色 (`#003DA5`)
   - 羽田機場 → 東京單軌藍色 (`#0071BC`)
   - 銀座 → 東京 Metro 藍綠色 (`#149BDF`)

#### 圖標規範
- **尺寸**:
  - Tier 1: `40x40px` (最大)
  - Tier 2: `32x32px`
  - Tier 3-5: `24x24px`
- **形狀**: 圓角矩形 (`border-radius: 8px`)
- **邊框**: 白色 `2px solid #FFFFFF`
- **陰影**: `box-shadow: 0 2px 8px rgba(0,0,0,0.15)`
- **圖示**: 使用 emoji `🚇` 或鐵道公司 Logo (如有授權)

---

## 🔗 Hub 聚合機制 (Hub Aggregation)

### 自動聚合規則
- **觸發條件**: 節點標記為 `display_tier <= 2` 且 `is_hub = true`
- **聚合範圍**: 220 公尺 (使用 Haversine 公式計算)
- **最大成員數**: 18 個子節點
- **排序邏輯**: 依距離由近至遠排序

### 展開邏輯
- **預設狀態**: Hub 聚合節點**收合顯示單一標記**
- **點擊展開**: 顯示所有成員車站 (最多 18 個)
- **展開視覺**: 成員節點以放射狀排列在 Hub 周圍

### 後台管理功能
- **手動設置**: 後台可指定任意節點為 Hub
- **調整範圍**: 可自訂聚合半徑 (預設 220m)
- **解除聚合**: 可將 Hub 轉為一般節點顯示
- **優先級調整**: 可手動變更 `display_tier` 層級

---

## 🗺️ Zoom-Based 顯示邏輯

### 核心函數

```typescript
/**
 * 判斷節點是否應該顯示 (Marker 可見性)
 */
function shouldShowNode(node: Node, currentZoom: number): boolean {
  return currentZoom >= node.min_zoom_level;
}

/**
 * 判斷節點名稱是否應該顯示 (Label 可見性)
 */
function shouldShowLabel(node: Node, currentZoom: number): boolean {
  // Tier 1: 永遠顯示名稱
  if (node.display_tier === 1) return true;

  // Tier 2: Zoom >= 12 顯示
  if (node.display_tier === 2) return currentZoom >= 12;

  // Tier 3: Zoom >= 14 顯示
  if (node.display_tier === 3) return currentZoom >= 14;

  // Tier 4-5: Zoom >= 15 顯示
  return currentZoom >= 15;
}

/**
 * 取得節點主要鐵道公司顏色
 */
function getNodeColor(node: Node): string {
  const operatorPriority = [
    { name: 'JR東日本', color: '#00AC4E' },
    { name: '東京メトロ', color: '#149BDF' },
    { name: '東京都交通局', color: '#B6007A' },
    { name: '京急電鉄', color: '#E31E24' },
    { name: '東武鉄道', color: '#003DA5' },
    { name: '京成電鉄', color: '#003DA5' },
  ];

  // 特殊案例處理
  if (node.id.includes('Narita')) return '#003DA5'; // 成田機場
  if (node.id.includes('Haneda')) return '#0071BC'; // 羽田機場

  // 從 node.operators 找第一個匹配的公司
  for (const op of operatorPriority) {
    if (node.operators?.some(o => o.includes(op.name))) {
      return op.color;
    }
  }

  // 預設灰色
  return '#6B7280';
}
```

### Zoom 層級對應表

| Zoom Level | 可見節點層級 | 說明 |
|-----------|------------|------|
| 1-11 | Tier 1 only | 僅超級樞紐 (關東全區) |
| 12-13 | Tier 1-2 | 主要樞紐開始顯示 |
| 14 | Tier 1-3 | 次要樞紐開始顯示 |
| 15 | Tier 1-4 | 常用車站開始顯示 |
| 16+ | Tier 1-5 | 所有車站顯示 |

---

## ⚡ 效能最佳化規則

### Viewport 虛擬化
- **使用 Hook**: `useViewportBounds` + `useVisibleMarkers`
- **原理**: 只渲染當前 Viewport 內的節點
- **效能指標**: 1000+ 節點下維持 60fps

### Memoization 策略
```typescript
// ✅ 正確: 使用 useMemo 快取過濾結果
const visibleNodes = useMemo(() => {
  return allNodes.filter(n => shouldShowNode(n, zoom));
}, [allNodes, zoom]);

// ✅ 正確: 使用 memo 包裝高頻渲染元件
const NodeMarker = memo(({ node, zoom }) => {
  // ...
});
```

### 避免過度渲染
- ❌ 禁止在 `map.on('move')` 中直接操作 DOM
- ❌ 禁止在每次 render 時重新計算顏色
- ✅ 使用 `zoomend` 事件而非 `zoom` 事件
- ✅ 預先計算並快取節點顏色於資料庫

---

## 🎯 其他圖層顯示規則

### L1 Places Layer (商業 POI)
- **顯示條件**: `Zoom >= 16`
- **原因**: 避免低 Zoom 時地圖過度擁擠
- **圖標規範**:
  - 合作店家: 金色漸層 `24x24px` + ⭐ emoji
  - 一般店家: 分類顏色圓點 `10x10px`

### 其他圖層
- **TrainLayer** (列車即時位置): 根據需求顯示
- **PedestrianLayer** (行人路線): `Zoom >= 17`
- **RouteLayer** (導航路線): 永遠顯示 (如有啟用)

---

## 🗄️ 資料庫 Schema 要求

### nodes 表必要欄位
```sql
CREATE TABLE nodes (
  id UUID PRIMARY KEY,
  name JSONB NOT NULL, -- 多語系名稱
  type TEXT NOT NULL,
  location GEOMETRY(Point, 4326) NOT NULL,

  -- [新增] 顯示層級控制欄位
  display_tier INTEGER DEFAULT 5, -- 1-5 層級
  min_zoom_level INTEGER DEFAULT 16, -- 最小顯示 Zoom
  daily_passengers INTEGER, -- 每日乘客數 (用於 Tier 4 排序)

  -- Hub 聚合相關
  is_hub BOOLEAN DEFAULT false,
  parent_hub_id UUID REFERENCES nodes(id),
  hub_aggregation_radius INTEGER DEFAULT 220, -- 聚合半徑 (公尺)

  -- 鐵道公司資訊
  operators JSONB, -- ["JR東日本", "東京メトロ"]
  primary_operator TEXT, -- 主要經營公司
  brand_color TEXT, -- 預先計算的品牌色 (HEX)

  -- 其他欄位...
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- 索引優化
CREATE INDEX idx_nodes_display_tier ON nodes(display_tier);
CREATE INDEX idx_nodes_zoom_level ON nodes(min_zoom_level);
CREATE INDEX idx_nodes_parent_hub ON nodes(parent_hub_id);
```

### Tier 1 節點初始化範例
```sql
-- 上野站 (JR 綠色)
UPDATE nodes SET
  display_tier = 1,
  min_zoom_level = 1,
  is_hub = true,
  primary_operator = 'JR東日本',
  brand_color = '#00AC4E',
  operators = '["JR東日本", "東京メトロ"]'::jsonb
WHERE name->>'ja' = '上野駅';

-- 成田機場 (京成藍色)
UPDATE nodes SET
  display_tier = 1,
  min_zoom_level = 1,
  is_hub = true,
  primary_operator = '京成電鉄',
  brand_color = '#003DA5',
  operators = '["京成電鉄", "JR東日本"]'::jsonb
WHERE id = 'odpt:Station:Airport.Narita';
```

---

## ✅ 開發檢查清單 (Checklist)

### 新增節點時
- [ ] 設置正確的 `display_tier` (1-5)
- [ ] 設置對應的 `min_zoom_level`
- [ ] 填寫 `operators` 陣列
- [ ] 計算並設置 `brand_color`
- [ ] 如為 Hub，設置 `is_hub = true`

### 修改地圖 UI 時
- [ ] 確保 Zoom 邏輯符合五層級規範
- [ ] Hub 名稱僅顯示地名 (無公司後綴)
- [ ] 顏色使用 `brand_color` 欄位
- [ ] 使用 `useMemo` 避免過度計算
- [ ] Viewport 虛擬化正常運作

### 效能測試
- [ ] 1000+ 節點下流暢度 (60fps)
- [ ] Zoom 切換無卡頓
- [ ] Hub 展開/收合動畫流暢

---

## 📚 相關檔案

- **前端元件**: `src/components/map/MapContainer.tsx`
- **Node Layer**: `src/components/map/HubNodeLayer.tsx`
- **L1 Layer**: `src/components/map/L1Layer.tsx`
- **Hooks**: `src/hooks/map/useViewportBounds.ts`, `src/hooks/map/useVisibleMarkers.ts`
- **資料庫 Migration**: `supabase/migrations/YYYYMMDDHHMMSS_add_display_tier.sql`

---

## 🚨 重要提醒

1. **Tier 1 節點永遠顯示名稱** - 任何 Zoom 層級都不可隱藏
2. **Hub 名稱不含公司資訊** - 僅地名 (例: `上野` 而非 `JR上野站`)
3. **顏色由主要公司決定** - 嚴格遵循優先級規則
4. **效能優先** - 務必使用 Viewport 虛擬化
5. **後台可調整** - 所有規則可透過後台人工覆寫

---

*此 Skill 最後更新: 2026-01-24*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/losangeles1156) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
