---
name: frontend-antd-components
description: Use when working with ANTD components, theme tokens, icons, forms, or feedback components (message/notification/modal)
metadata:
  author: ntq78
---

# Frontend: ANTD Components

All new components must use Ant Design (ANTD) with theme tokens. Never use Radix UI, TailwindCSS, or hardcoded values for new development.

## Theme Tokens (Required)

**Always use ANTD theme tokens** instead of hardcoded values:

```typescript
import { theme } from 'antd';

function MyComponent() {
  const { token } = theme.useToken();

  return (
    <div style={{
      padding: token.paddingXL,
      color: token.colorPrimary,
      fontSize: token.fontSizeLG,
      borderRadius: token.borderRadiusLG,
    }}>
      Content
    </div>
  );
}
```

**Common Token Categories:**

- **Colors:** `token.colorPrimary`, `token.colorError`, `token.colorSuccess`, `token.colorBorder`
- **Spacing:** `token.paddingSM`, `token.marginMD`, `token.paddingLG`, `token.marginXL`
- **Typography:** `token.fontSize`, `token.fontSizeLG`, `token.fontSizeHeading1`
- **Borders:** `token.borderRadius`, `token.borderRadiusLG`

## Typography Components

Use ANTD Typography components for text content:

```typescript
import { Typography, theme } from 'antd';

const { Title, Paragraph, Text } = Typography;

function MyComponent() {
  const { token } = theme.useToken();

  return (
    <>
      <Title level={1}>Page Title</Title>
      <Paragraph type="secondary">Description text</Paragraph>
      <Text strong>Bold text</Text>
    </>
  );
}
```

## App.useApp Pattern (Message/Notification/Modal)

**Always use `App.useApp()` hook** for feedback components:

```typescript
import { App } from 'antd';

function MyComponent() {
  const { message, notification, modal } = App.useApp();

  const handleSuccess = () => {
    message.success('Operation successful!');
  };

  const handleNotify = () => {
    notification.info({
      message: 'Update Available',
      description: 'A new version is ready.',
    });
  };

  return <button onClick={handleSuccess}>Save</button>;
}
```

**Note:** Your app must be wrapped with `<App>` in the provider. See `src/providers/antd/Provider_ANTD.tsx`.

## Modal Confirmations (Async Pattern)

**Always use async onOk with mutateAsync** for delete confirmations and critical actions:

```typescript
import { App } from 'antd';
import { useM_Files_Delete } from "@/hooks/Files/useM_Files_Delete";

function MyComponent() {
    const { modal } = App.useApp();
    const mFiles_Delete = useM_Files_Delete();

    const handleDelete = (fileId: string, fileName: string) => {
        modal.confirm({
            title: "Delete File",
            content: `Are you sure you want to delete "${fileName}"?`,
            okText: "Delete",
            okType: "danger",
            onOk: async () => {
                await mFiles_Delete.mutation.mutateAsync({ fileId });
            },
        });
    };

    return <button onClick={() => handleDelete('file_1', 'document.pdf')}>Delete</button>;
}
```

**Why async onOk matters:**

- Modal stays open until mutation completes
- Loading spinner shows automatically on OK button
- Errors prevent modal from closing
- Success closes modal automatically

## Icon Usage

**Primary:** Use `@ant-design/icons` for all icons:

```typescript
import { PlusOutlined, EditOutlined, ProjectOutlined } from '@ant-design/icons';

function MyComponent() {
  const { token } = theme.useToken();

  return (
    <>
      <Button icon={<PlusOutlined />}>Create</Button>
      <ProjectOutlined style={{ fontSize: 18, color: token.colorPrimary }} />
    </>
  );
}
```

**Alternative:** Use `lucide-react` only when ANTD doesn't have a suitable icon. Never mix both in the same component.

## Form Handling

Use ANTD Form components with validation rules:

```typescript
import { Form, Input, Button } from 'antd';

function MyComponent() {
  const [form] = Form.useForm();

  const handleSubmit = () => {
    form.validateFields().then((values) => {
      // Handle submission
    });
  };

  return (
    <Form form={form} layout="vertical">
      <Form.Item
        label="Name"
        name="name"
        rules={[
          { required: true, message: 'Please enter a name' },
          { min: 1, max: 255, message: 'Name must be 1-255 characters' },
        ]}
      >
        <Input placeholder="Enter name..." />
      </Form.Item>

      <Button type="primary" onClick={handleSubmit}>
        Submit
      </Button>
    </Form>
  );
}
```

## CSS/Styling Rules

- **No separate CSS/SCSS files** for new components
- **Use:** ANTD components + inline styles with theme tokens
- **Legacy:** Existing TailwindCSS components remain as-is (no refactoring required)

## Deprecated Props (Avoid)

Some ANTD props are deprecated and cause console warnings. Always use the new replacement props.

**Common Deprecated Props:**

- ❌ Card: `bodyStyle` → ✅ Use `styles={{ body: {...} }}`
- ❌ Modal: `destroyOnClose` → ✅ Use `destroyOnHidden`
- ❌ Divider: `orientation="left/right"` → ✅ Use `titlePlacement="left/right"` (for text positioning)

**Examples:**

```typescript
// ✅ Good - Use styles.body
<Card styles={{ body: { padding: token.paddingSM } }}>Content</Card>

// ✅ Good - Use destroyOnHidden
<Modal destroyOnHidden>Content</Modal>

// ✅ Good - Use titlePlacement for text positioning
<Divider titlePlacement="left">Section Title</Divider>
```

## Component Tokens (Configured in Provider_ANTD)

Button and other components have **component-level tokens** set in `Provider_ANTD.tsx`. Do NOT override these with inline styles — use ANTD's `size` prop and let the tokens handle it:

```typescript
// ❌ Bad — overrides component tokens with inline styles
<Button type="primary" style={{ borderRadius: 9999, fontSize: 16, fontWeight: 600, paddingInline: 24 }}>

// ✅ Good — component tokens handle radius, font, padding automatically
<Button type="primary" size="large">
```

**Configured Button sizes** (via component tokens):
- `size="large"` → 48px height, 16px font, 24px padding, pill shape
- `size="middle"` (default) → 32px height, 12px font, 16px padding, pill shape

## Anti-Pattern Detection

Check for these violations before proceeding:

1. ❌ Hardcoded colors/spacing → ✅ Use `theme.useToken()` and `token.*`
2. ❌ `import { message } from 'antd'` → ✅ `const { message } = App.useApp()`
3. ❌ Plain HTML (`<h1>`, `<p>`) → ✅ ANTD Typography (`<Title>`, `<Paragraph>`)
4. ❌ Missing `theme.useToken()` hook → ✅ Import and use in component
5. ❌ Mixed icon libraries → ✅ Choose one (prefer `@ant-design/icons`)
6. ❌ New Radix/Tailwind → ✅ Use ANTD only
7. ❌ Deprecated props (`bodyStyle`, `destroyOnClose`, `orientation` on Divider) → ✅ Use new props
8. ❌ Inline styles that duplicate component tokens (borderRadius, fontSize, fontWeight, padding on Button) → ✅ Use `size` prop

If any violations are detected, point them out and ask the user to confirm before proceeding.

## Reference

For complete practices: `docs/spark/frontend/my-vite-app/practices.md` - "UI Component Library"

For detailed examples: `.claude/skills/frontend-antd-components/examples.md`

<!-- Last compacted: 2025-11-16 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
