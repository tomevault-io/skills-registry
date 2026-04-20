---
name: flat-composition
description: Skill Flat Composition Pattern cho frontend. Khong long component, zero props, container pattern. Tu dong kich hoat khi viet components, pages, hoac UI Use when this capability is needed.
metadata:
  author: maxlogvn
---

# Flat Composition Skill

Skill này hướng dẫn Flat Composition Design Pattern và Component Organization Rules cho dự án AiCMR.

## Design System Reference

**Nguồn chính thức**: `/code/AiCMR/docs/DESIGN_ADN.md`

Thông tin Design System được quản lý tại file trên. Skill này reference đến file đó thay vì hardcode.

### Quick Reference (tham khảo từ DESIGN_ADN.md)

**Màu sắc OKLCH:**
- Semantic: `bg-primary` (orange), `bg-secondary` (green), `bg-accent` (purple), `bg-destructive` (red)
- Surface: `bg-background`, `bg-card`, `bg-popover`, `bg-muted`
- Chart: `bg-chart-1` đến `bg-chart-5`

**Typography:**
- Headings & UI: Roboto Condensed (300-900)
- Body: Roboto (100-900)
- Code: Source Code Pro
- Type Scale: 48/36/30/24/20/18/16/14/12px (ratio 1.25)

**Spacing:** Đơn vị 4px base

**Border Radius:** Sharp edges (0rem) - không bo góc

**Shadows:** `shadow-design-sm`, `shadow-design`, `shadow-design-lg`

## Flat Composition Principles

### Nguyên tắc cốt lõi

```
❌ NESTED (Tránh):
layout → Header → Navigation → Logo
       ↓         ↓
     Footer   AuthSection

✅ FLAT (Khuyến khích):
layout → HeaderContainer
        ├─→ Logo (direct)
        ├─→ Navigation (direct)
        ├─→ AuthSection (direct)
        └─→ FooterContainer
            ├─→ Brand (direct)
            ├─→ ProductLinks (direct)
            └─→ CommunityLinks (direct)
```

### Rules

1. **Không lồng component**: Component không được import component từ cùng feature folder
2. **Chỉ import UI components**: Chỉ import từ `/components/ui`
3. **Zero Props Pattern**: Mỗi component tự fetch data, không nhận props chain
4. **Container Pattern**: Layout → Container → Components (max 2 levels)

### Component Template

```tsx
// example-component.tsx
'use client'

export function ExampleComponent() {
  // ✅ Self-fetch data
  const { user } = useAuth()
  const pathname = usePathname()

  // ✅ Self-manage logic
  if (pathname?.startsWith('/settings')) return null

  // ✅ Render UI (chỉ từ /components/ui)
  // ✅ Sử dụng design tokens từ DESIGN_ADN.md
  return (
    <div className="p-spacing-md">
      <Avatar src={user.avatar} />
      <h1 className="font-condensed text-h1 text-foreground">{user.username}</h1>
      <Button variant="default" className="bg-primary text-primary-foreground">
        Edit Profile
      </Button>
    </div>
  )
}
```

### Anti-Patterns

**❌ Deep Nesting:**
```tsx
<Dashboard>
  <DashboardSidebar>
    <SidebarUserSection>
      <UserAvatar />
    </SidebarUserSection>
  </DashboardSidebar>
</Dashboard>
```

**✅ Flat:**
```tsx
<DashboardLayout>
  <Sidebar />
  <DashboardHeader />
  <ContentArea />
</DashboardLayout>
```

**❌ Props Drilling:**
```tsx
<Header
  user={user}
  onLogout={handleLogout}
  links={links}
  isLoading={isLoading}
/>
```

**✅ Zero Props:**
```tsx
<HeaderContainer />  // Self-fetch all data
```

## Component Organization

### Location Rules

```
/components/
├── ui/              ← Generic UI (Button, Input, Dialog...)
├── shared/          ← Cross-feature (dùng >=3 places)
├── layout/          ← Layout-specific
│   ├── header/      ← Chỉ header-related
│   └── footer/      ← Chỉ footer-related
├── auth/            ← Authentication components
├── dashboard/       ← Dashboard-specific
├── landing/         ← Landing page
└── [feature]/       ← Feature-based
```

### Quy tắc đặt Components

| Component Type | Vị trí | Ví dụ |
|----------------|--------|-------|
| Chỉ dùng trong header | `layout/header/` | `theme-toggle.tsx` |
| Chỉ dùng trong footer | `layout/footer/` | `brand.tsx` |
| Dùng trong cả header & footer | `layout/` | `container.tsx` |
| Dùng trong auth | `auth/` | `auth-provider.tsx` |
| Dùng trong dashboard | `dashboard/` | `dashboard-header.tsx` |
| Dùng trong nhiều pages/features | `shared/` | `rank-badge.tsx` |
| Reusable UI | `ui/` | `button.tsx` |

### Workflow Di Chuyển Component

1. **Kiểm tra vị trí hiện tại:**
   ```bash
   ls -la components/[component-name].tsx
   ```

2. **Tìm tất cả nơi sử dụng:**
   ```bash
   grep -r "ComponentName" frontend --include="*.tsx" | grep -v node_modules
   ```

3. **Phân loại:**
   - Chỉ dùng 1 feature → Di vào feature folder
   - Dùng nhiều features → Giữ root hoặc shared/
   - Không dùng đâu → XÓA

4. **Di chuyển:**
   ```bash
   mv [old-path] [new-path]
   ```

5. **Cập nhật imports:**
   ```tsx
   // Trước
   import { ThemeToggle } from '@/components/theme-toggle'
   // Sau (cùng folder)
   import { ThemeToggle } from './theme-toggle'
   // Sau (khác folder)
   import { ThemeToggle } from '@/components/layout/header/theme-toggle'
   ```

## Decision Tree: Khi nào tạo Component?

### Question 1: Component này có logic riêng không?

```
YES → Tạo component riêng
NO  → Giữ inline
```

### Question 2: Component này dùng ở bao nhiêu places?

```
Chỉ 1 place → Có thể inline, trừ khi:
  - Logic phức tạp (>50 lines)
  - Cần test riêng
  - Reusable trong tương lai

2-3 places → Tạo component, đặt ở:
  - Feature folder nếu cùng feature
  - /components root nếu cross-feature

4+ places → Tạo component ở /components/ root
```

### Question 3: Component này có phụ thuộc component khác không?

```
YES → Cần refactor:
  - Trích xuất shared logic → hook hoặc utility
  - Merge components nếu hợp lý
  - Tạo container pattern

NO  → Có thể Flat
```

## Workflow: Thiết kế Flat Component

### Phase 1: Design & Phân tích

**Step 1: Vẽ Component Hierarchy**
```
[Page/Feature]
    ↓
[Container] ← Manages state, layout structure
    ↓
[Component A] [Component B] [Component C] ← Independent
```

**Step 2: Identify Responsibilities**

Mỗi component làm **1 việc rõ ràng**.

**Step 3: Define Dependencies**

| Scenario | Solution |
|----------|----------|
| Share data across components | Context hoặc lift state |
| Component needs same data | Duplicate fetch (OK!) |
| Complex component logic | Tách helper hooks |
| Component chỉ dùng 1 lần | Inline hoặc tách nếu cần test |

### Phase 2: Implementation

**Step 1: Create Folder Structure**
```bash
mkdir -p components/profile
cd components/profile

# Structure:
components/profile/
├── profile-header.tsx
├── profile-info.tsx
├── profile-stats.tsx
├── profile-menu.tsx
├── container.tsx
└── index.ts
```

**Step 2: Implement Components (Zero Props)**

```tsx
// profile-header.tsx
'use client'

export function ProfileHeader() {
  const { user } = useAuth()
  const pathname = usePathname()

  if (pathname?.startsWith('/settings')) return null

  return (
    <div className="profile-header p-spacing-md">
      <Avatar src={user.avatar} />
      <h1 className="font-condensed text-h3">{user.username}</h1>
      <Button variant="default" className="bg-primary text-primary-foreground">
        Edit Profile
      </Button>
    </div>
  )
}
```

**Step 3: Create Container**
```tsx
// container.tsx
'use client'

export function ProfileContainer() {
  return (
    <div className="profile-container space-y-spacing-md">
      <ProfileHeader />
      <ProfileInfo />
      <ProfileStats />
      <ProfileMenu />
    </div>
  )
}
```

**Step 4: Barrel Export**
```tsx
// index.ts
export { ProfileContainer } from './container'
export { ProfileHeader } from './profile-header'
export { ProfileInfo } from './profile-info'
export { ProfileStats } from './profile-stats'
export { ProfileMenu } from './profile-menu'
```

**Step 5: Use in Page**
```tsx
// app/profile/page.tsx
import { ProfileContainer } from '@/components/profile/container'

export default function ProfilePage() {
  return <ProfileContainer />
}
```

### Phase 3: Verification

```bash
# Check component không import từ cùng folder
grep -rn "from '\\./" components/[feature]" --include="*.tsx"
# Should return NOTHING

# Check props drilling
grep -rn "interface Props" components/[feature] --include="*.tsx" -A 5
# ✅ Good: 1-3 props
# ❌ Bad: 5+ props
```

## Design Checklist

Trước khi commit code:

- [ ] **No nesting**: Components không import từ cùng feature folder
- [ ] **Zero props**: Components tự fetch data (hoặc chỉ styling props)
- [ ] **Container thin**: Container 50-100 lines max
- [ ] **Clear responsibility**: Mỗi component làm 1 việc rõ ràng
- [ ] **Independent testable**: Mỗi component có thể test riêng
- [ ] **Absolute imports**: Dùng `@/components/...` cho cross-folder
- [ ] **Design tokens**: Sử dụng đúng tokens từ `/code/AiCMR/docs/DESIGN_ADN.md`

## Metrics

### Good Indicators:
- Component file size: 50-200 lines
- Props count: 0-3 props
- Import depth: Max 2 levels
- Coupling: Low

### Warning Signs:
- Component >300 lines → Cần split
- Props >5 → Consider context hoặc self-fetch
- Import chain >3 levels → Flatten structure

## Code Templates với Design Tokens

**Lưu ý:** Design tokens đầy đủ tại `/code/AiCMR/docs/DESIGN_ADN.md`

### Button

```tsx
import { Button } from "@/components/ui/button"

<Button variant="default" className="bg-primary text-primary-foreground">
  Lưu thay đổi
</Button>

<Button variant="destructive" className="bg-destructive text-destructive-foreground">
  Xóa
</Button>

<Button variant="outline" className="border-2">
  Hủy
</Button>
```

### Card

```tsx
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card"

<Card className="bg-card shadow-design">
  <CardHeader>
    <CardTitle className="font-condensed text-h3">Tiêu đề</CardTitle>
  </CardHeader>
  <CardContent>
    <p className="text-body text-foreground">Nội dung</p>
  </CardContent>
</Card>
```

### Typography

```tsx
<h1 className="font-condensed text-h1 text-foreground">Display</h1>
<h2 className="font-condensed text-h2 text-foreground">Heading 1</h2>
<p className="font-body text-body text-foreground">Body text</p>
<code className="font-mono text-small">Code snippet</code>
```

### Spacing

```tsx
<div className="p-spacing-md gap-spacing-sm flex">
  {/* padding: 16px, gap: 8px */}
</div>

<div className="m-spacing-lg space-y-spacing-md">
  {/* margin: 24px, space-y: 16px */}
</div>
```

## Examples

### Example 1: Blog Page

```
[BlogPage]
    ↓
[BlogContainer]
    ├─→ [BlogHeader]         ← Self-fetch post data
    ├─→ [BlogContent]        ← Render markdown
    ├─→ [BlogSidebar]        ← Categories, related posts
    └─→ [BlogComments]       ← Comments section
```

### Example 2: Settings Page

```
[SettingsPage]
    ↓
[SettingsContainer]
    ├─→ [SettingsNav]         ← Tabs (self-manage state)
    ├─→ [ProfileForm]         ← Self-save
    ├─→ [SecurityForm]        ← Self-save
    └─→ [NotificationsForm]  ← Self-save
```

### Example 3: Di chuyển ThemeToggle

**Analysis:**
- Chỉ dùng trong: `layout/header/container.tsx`, `layout/header/mobile-menu.tsx`

**Action:**
```bash
mv components/theme-toggle.tsx components/layout/header/theme-toggle.tsx

# Update imports
# container.tsx: import { ThemeToggle } from './theme-toggle'
# mobile-menu.tsx: import { ThemeToggle } from './theme-toggle'
```

## Common Mistakes

1. **Quên update imports sau khi move** → LỖI IMPORT
2. **Dùng relative path thay vì absolute** → Khó maintain
3. **Di chuyển component cross-feature vào feature folder** → Sai vị trí
4. **Quên xóa file cũ sau khi copy** → Duplicate files
5. **Hardcode design tokens** → Nên đọc từ `/code/AiCMR/docs/DESIGN_ADN.md`

---

**Phiên bản:** v1.1
**Nguồn:** FLAT_COMPOSITION_DESIGN.md + COMPONENT_WORKFLOW.md + DESIGN_ADN.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxlogvn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
