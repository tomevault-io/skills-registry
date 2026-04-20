---
name: zaui-ui-builder
description: Builds UI for Zalo Mini App using zmp-ui (ZaUI) components. Use when asked to create pages, components, forms, modals, or any UI for ZMP projects.
metadata:
  author: tienchinh21
---

# ZaUI UI Builder

Specialized skill for building Zalo Mini App UI using zmp-ui (ZaUI) components v1.11.0.

## When to Use

- "Tạo page/màn hình mới"
- "Build form/modal/list/bottom sheet"
- "Tạo component theo ZaUI"
- Any UI building task for Zalo Mini App

## Core Principle: ZaUI First

**Always prioritize zmp-ui components over custom implementations.**

- Use ZaUI for all standard UI patterns
- Use Tailwind CSS only for spacing, layout adjustments
- Never override ZaUI design tokens unless explicitly required
- Follow Zalo Design System (ZDS) patterns

## Required Page Structure

Every ZMP page MUST follow this structure:

```tsx
import React from 'react';
import { Page, Header } from 'zmp-ui';

const MyPage: React.FC = () => {
  return (
    <Page>
      <Header title="Page Title" showBackIcon />
      {/* Page content */}
    </Page>
  );
};

export default MyPage;
```

**App Root Structure:**

```tsx
import React from 'react';
import { App, SnackbarProvider } from 'zmp-ui';

const MyApp: React.FC = () => {
  return (
    <App>
      <SnackbarProvider>
        {/* Router and pages */}
      </SnackbarProvider>
    </App>
  );
};
```

## Component Reference

### Layout Components

| Component | Purpose | Key Props |
|-----------|---------|-----------|
| `App` | Root container (required) | - |
| `Page` | Page wrapper (required) | `className`, `hideScrollbar` |
| `Header` | Navigation header | `title`, `showBackIcon`, `onBackClick` |
| `BottomNavigation` | Tab bar | `activeKey`, `onChange` |
| `Tabs` | Content tabs | `activeKey`, `onChange` |
| `ZMPRouter` | Router wrapper | - |
| `AnimationRoutes` | Animated route transitions | - |

**Header Example:**
```tsx
<Header 
  title="Tiêu đề" 
  showBackIcon 
  onBackClick={() => navigate(-1)}
/>
```

**BottomNavigation Example:**
```tsx
<BottomNavigation
  activeKey={activeTab}
  onChange={(key) => setActiveTab(key)}
>
  <BottomNavigation.Item key="home" label="Trang chủ" icon={<Icon icon="zi-home" />} />
  <BottomNavigation.Item key="profile" label="Cá nhân" icon={<Icon icon="zi-user" />} />
</BottomNavigation>
```

### Form Components

| Component | Purpose | Key Props |
|-----------|---------|-----------|
| `Button` | Action buttons | `variant`, `size`, `loading`, `disabled`, `fullWidth` |
| `Input` | Text input | `label`, `placeholder`, `value`, `onChange`, `status`, `errorText` |
| `Password` | Password input | Same as Input |
| `Search` | Search input | `placeholder`, `onSearch` |
| `TextArea` | Multi-line input | `label`, `maxLength`, `showCount` |
| `OTP` | OTP input | `length`, `onChange` |
| `Select` | Dropdown select | `label`, `placeholder`, `options`, `value`, `onChange` |
| `Picker` | Bottom picker | `data`, `value`, `onChange` |
| `DatePicker` | Date selection | `value`, `onChange`, `min`, `max` |
| `Switch` | Toggle switch | `checked`, `onChange` |
| `Checkbox` | Checkbox | `checked`, `onChange`, `label` |
| `Radio` | Radio button | `checked`, `onChange`, `label` |
| `Slider` | Value slider | `value`, `onChange`, `min`, `max` |

**Button Variants:**
```tsx
<Button variant="primary">Primary</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="tertiary">Tertiary</Button>
<Button variant="text">Text</Button>
<Button loading>Loading...</Button>
<Button fullWidth>Full Width</Button>
```

**Input with Validation:**
```tsx
<Input
  label="Email"
  placeholder="Nhập email"
  value={email}
  onChange={(e) => setEmail(e.target.value)}
  status={error ? 'error' : 'default'}
  errorText={error}
/>
```

**Select Example:**
```tsx
<Select
  label="Chọn thành phố"
  placeholder="Chọn..."
  options={[
    { value: 'hcm', label: 'TP. Hồ Chí Minh' },
    { value: 'hn', label: 'Hà Nội' },
  ]}
  value={city}
  onChange={(value) => setCity(value)}
/>
```

### Display Components

| Component | Purpose | Key Props |
|-----------|---------|-----------|
| `Avatar` | User avatar | `src`, `size`, `online` |
| `List` | List container | - |
| `List.Item` | List item | `title`, `subTitle`, `prefix`, `suffix` |
| `Swiper` | Carousel/slider | `autoplay`, `duration` |
| `Progress` | Progress bar | `percent`, `showText` |
| `Spinner` | Loading spinner | `visible`, `logo` |
| `Text` | Typography | `size`, `bold` |
| `Icon` | Icons | `icon` (zi-* format) |
| `Calendar` | Calendar view | `value`, `onChange` |
| `ImageViewer` | Image gallery | `images`, `visible` |

**List Example:**
```tsx
<List>
  <List.Item
    title="Tên người dùng"
    subTitle="Mô tả phụ"
    prefix={<Avatar src={avatarUrl} />}
    suffix={<Icon icon="zi-chevron-right" />}
    onClick={() => handleClick()}
  />
</List>
```

**Swiper Example:**
```tsx
<Swiper autoplay duration={3000}>
  <Swiper.Slide>
    <img src={banner1} alt="Banner 1" />
  </Swiper.Slide>
  <Swiper.Slide>
    <img src={banner2} alt="Banner 2" />
  </Swiper.Slide>
</Swiper>
```

### Overlay Components

| Component | Purpose | Key Props |
|-----------|---------|-----------|
| `Modal` | Dialog/Alert | `visible`, `title`, `onClose`, `actions` |
| `Sheet` | Bottom sheet | `visible`, `onClose`, `height` |
| `ActionSheet` | Action options | `visible`, `actions`, `onClose` |
| `useSnackbar` | Toast notifications | `openSnackbar({ text, type })` |

**Modal Example:**
```tsx
<Modal
  visible={showModal}
  title="Xác nhận"
  onClose={() => setShowModal(false)}
  actions={[
    { text: 'Hủy', close: true },
    { text: 'Đồng ý', close: true, highLight: true, onClick: handleConfirm },
  ]}
>
  <p>Bạn có chắc chắn muốn thực hiện?</p>
</Modal>
```

**Sheet Example:**
```tsx
<Sheet
  visible={showSheet}
  onClose={() => setShowSheet(false)}
  height={300}
  title="Chi tiết"
>
  {/* Sheet content */}
</Sheet>
```

**ActionSheet Example:**
```tsx
<ActionSheet
  visible={showActions}
  onClose={() => setShowActions(false)}
  actions={[
    { text: 'Chỉnh sửa', onClick: handleEdit },
    { text: 'Xóa', danger: true, onClick: handleDelete },
  ]}
/>
```

**Snackbar (Toast) Example:**
```tsx
import { useSnackbar } from 'zmp-ui';

const MyComponent = () => {
  const { openSnackbar } = useSnackbar();
  
  const showSuccess = () => {
    openSnackbar({
      text: 'Thành công!',
      type: 'success',
      duration: 2000,
    });
  };
  
  const showError = () => {
    openSnackbar({
      text: 'Có lỗi xảy ra',
      type: 'error',
    });
  };
};
```

## Icons

ZaUI uses `zi-*` icon format:

```tsx
import { Icon } from 'zmp-ui';

<Icon icon="zi-home" />
<Icon icon="zi-user" />
<Icon icon="zi-chevron-right" />
<Icon icon="zi-close" />
<Icon icon="zi-search" />
<Icon icon="zi-plus" />
<Icon icon="zi-minus" />
<Icon icon="zi-check" />
```

Full icon list: https://miniapp.zaloplatforms.com/documents/zaui/foundation/icons/

## Page Templates

### List Page Template

```tsx
import React, { useState } from 'react';
import { Page, Header, List, Avatar, Icon, Spinner } from 'zmp-ui';

interface Item {
  id: string;
  title: string;
  subTitle: string;
  avatar?: string;
}

const ListPage: React.FC = () => {
  const [loading, setLoading] = useState(false);
  const [items, setItems] = useState<Item[]>([]);

  if (loading) {
    return (
      <Page>
        <Header title="Danh sách" showBackIcon />
        <div className="flex items-center justify-center h-64">
          <Spinner visible />
        </div>
      </Page>
    );
  }

  return (
    <Page>
      <Header title="Danh sách" showBackIcon />
      <List>
        {items.map((item) => (
          <List.Item
            key={item.id}
            title={item.title}
            subTitle={item.subTitle}
            prefix={<Avatar src={item.avatar} />}
            suffix={<Icon icon="zi-chevron-right" />}
          />
        ))}
      </List>
    </Page>
  );
};
```

### Form Page Template

```tsx
import React, { useState } from 'react';
import { Page, Header, Input, Button, useSnackbar } from 'zmp-ui';

const FormPage: React.FC = () => {
  const { openSnackbar } = useSnackbar();
  const [loading, setLoading] = useState(false);
  const [formData, setFormData] = useState({
    name: '',
    phone: '',
  });
  const [errors, setErrors] = useState<Record<string, string>>({});

  const handleSubmit = async () => {
    // Validate
    const newErrors: Record<string, string> = {};
    if (!formData.name) newErrors.name = 'Vui lòng nhập tên';
    if (!formData.phone) newErrors.phone = 'Vui lòng nhập SĐT';
    
    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }

    setLoading(true);
    try {
      // Submit logic
      openSnackbar({ text: 'Gửi thành công!', type: 'success' });
    } catch (error) {
      openSnackbar({ text: 'Có lỗi xảy ra', type: 'error' });
    } finally {
      setLoading(false);
    }
  };

  return (
    <Page>
      <Header title="Thông tin" showBackIcon />
      <div className="p-4 space-y-4">
        <Input
          label="Họ tên"
          placeholder="Nhập họ tên"
          value={formData.name}
          onChange={(e) => setFormData({ ...formData, name: e.target.value })}
          status={errors.name ? 'error' : 'default'}
          errorText={errors.name}
        />
        <Input
          label="Số điện thoại"
          placeholder="Nhập số điện thoại"
          value={formData.phone}
          onChange={(e) => setFormData({ ...formData, phone: e.target.value })}
          status={errors.phone ? 'error' : 'default'}
          errorText={errors.phone}
        />
        <Button 
          fullWidth 
          variant="primary"
          loading={loading}
          onClick={handleSubmit}
        >
          Gửi
        </Button>
      </div>
    </Page>
  );
};
```

## State Patterns

### Page State Pattern

```tsx
type PageState = 'loading' | 'empty' | 'error' | 'success';

const MyPage: React.FC = () => {
  const [state, setState] = useState<PageState>('loading');
  const [data, setData] = useState<Data | null>(null);

  // Render based on state
  if (state === 'loading') return <LoadingView />;
  if (state === 'error') return <ErrorView onRetry={fetchData} />;
  if (state === 'empty') return <EmptyView />;
  return <DataView data={data} />;
};
```

## Styling Guidelines

### Do's ✓
- Use Tailwind for spacing: `p-4`, `mt-2`, `space-y-4`
- Use Tailwind for flex/grid: `flex`, `items-center`, `grid grid-cols-2`
- Use Tailwind for responsive: `md:flex-row`

### Don'ts ✗
- Don't override ZaUI colors with Tailwind color classes
- Don't use custom border-radius on ZaUI components
- Don't use `!important` to override ZaUI styles

## Documentation Discipline

**IMPORTANT:** If you need specific props, methods, or behavior not covered here:

1. Check official docs: https://miniapp.zaloplatforms.com/documents/zaui
2. Each component has detailed API documentation
3. Never guess props - verify from official docs

## Quick Reference Links

- Button: https://miniapp.zaloplatforms.com/documents/zaui/form/button
- Input: https://miniapp.zaloplatforms.com/documents/zaui/form/input
- Modal: https://miniapp.zaloplatforms.com/documents/zaui/overlay/modal
- Sheet: https://miniapp.zaloplatforms.com/documents/zaui/overlay/sheet
- List: https://miniapp.zaloplatforms.com/documents/zaui/display/list
- All components: https://miniapp.zaloplatforms.com/documents/zaui

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tienchinh21) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
