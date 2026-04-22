---
name: fluent2-react
description: Build UI prototypes using Microsoft Fluent 2 Design System with React components. Use this skill when creating any React UI, prototype, or frontend application. Triggers on requests to build UI, create components, prototype interfaces, or when Fluent/Fluent 2 is mentioned. Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# Fluent 2 React Prototyping

Build beautiful, accessible UIs using Microsoft's Fluent 2 design system with React.

## Setup

Install the package:
```bash
npm install @fluentui/react-components
# or
yarn add @fluentui/react-components
```

## App Root Setup

Always wrap your app with `FluentProvider`:

```tsx
import { FluentProvider, webLightTheme } from "@fluentui/react-components";

export default function App() {
  return (
    <FluentProvider theme={webLightTheme}>
      {/* Your app content */}
    </FluentProvider>
  );
}
```

**Available themes:** `webLightTheme`, `webDarkTheme`, `teamsLightTheme`, `teamsDarkTheme`

## Component Import Pattern

Import components from `@fluentui/react-components`:

```tsx
import {
  Button,
  Input,
  Card,
  Dialog,
  // ... other components
} from "@fluentui/react-components";
```

## Component Reference

**📚 Full component documentation:** [references/components/_index.md](references/components/_index.md)

### Quick Links by Category

**Setup & Styling**
- [FluentProvider](references/components/fluent-provider.md) - Theme provider
- [Styling & Theming](references/components/styling.md) - makeStyles, tokens
- [Icons](references/components/icons.md) - Fluent System Icons

**Buttons**
- [Button](references/components/button.md) | [CompoundButton](references/components/compound-button.md) | [ToggleButton](references/components/toggle-button.md) | [SplitButton](references/components/split-button.md) | [MenuButton](references/components/menu-button.md)

**Form Inputs**
- [Input](references/components/input.md) | [Textarea](references/components/textarea.md) | [Field](references/components/field.md) | [SearchBox](references/components/search-box.md) | [SpinButton](references/components/spin-button.md)
- [Checkbox](references/components/checkbox.md) | [Switch](references/components/switch.md) | [RadioGroup](references/components/radio-group.md) | [Slider](references/components/slider.md)

**Selection**
- [Dropdown](references/components/dropdown.md) | [Combobox](references/components/combobox.md) | [Select](references/components/select.md)
- [TagPicker](references/components/tag-picker.md) | [SwatchPicker](references/components/swatch-picker.md) | [ColorPicker](references/components/color-picker.md)

**Layout & Containers**
- [Card](references/components/card.md) | [Accordion](references/components/accordion.md) | [Divider](references/components/divider.md)
- [Dialog](references/components/dialog.md) | [Drawer](references/components/drawer.md) | [Popover](references/components/popover.md)

**Navigation**
- [Tablist](references/components/tablist.md) | [Menu](references/components/menu.md) | [Breadcrumb](references/components/breadcrumb.md)
- [NavDrawer](references/components/nav-drawer.md) | [Link](references/components/link.md) | [Tree](references/components/tree.md)

**Data Display**
- [Avatar](references/components/avatar.md) | [Persona](references/components/persona.md) | [Badge](references/components/badge.md) | [Tag](references/components/tag.md)
- [Table](references/components/table.md) | [DataGrid](references/components/data-grid.md) | [List](references/components/list.md)
- [Text](references/components/text.md) | [Image](references/components/image.md) | [Tooltip](references/components/tooltip.md)

**Feedback & Status**
- [Spinner](references/components/spinner.md) | [ProgressBar](references/components/progress-bar.md) | [Skeleton](references/components/skeleton.md)
- [MessageBar](references/components/message-bar.md) | [Toast](references/components/toast.md) | [Rating](references/components/rating.md)

**Advanced**
- [Carousel](references/components/carousel.md) | [TeachingPopover](references/components/teaching-popover.md)
- [Motion & Animation](references/components/motion.md) | [AriaLiveAnnouncer](references/components/aria-live-announcer.md)
- [Utilities & Hooks](references/components/utilities.md) | [All Exports](references/components/exports.md)

## Core Components Quick Reference

### Buttons
```tsx
<Button appearance="primary">Primary</Button>
<Button appearance="secondary">Secondary</Button>
<Button appearance="outline">Outline</Button>
<Button appearance="subtle">Subtle</Button>
<Button appearance="transparent">Transparent</Button>
<Button icon={<Icon />}>With Icon</Button>
```

### Form Inputs
```tsx
<Field label="Name">
  <Input placeholder="Enter name" />
</Field>

<Field label="Description">
  <Textarea placeholder="Enter description" />
</Field>

<Field label="Choice">
  <Dropdown placeholder="Select option">
    <Option>Option 1</Option>
    <Option>Option 2</Option>
  </Dropdown>
</Field>

<Checkbox label="Accept terms" />
<Switch label="Enable feature" />
<RadioGroup>
  <Radio value="a" label="Option A" />
  <Radio value="b" label="Option B" />
</RadioGroup>
```

### Layout & Containers
```tsx
<Card>
  <CardHeader header={<Text weight="semibold">Title</Text>} />
  <CardPreview>Preview content</CardPreview>
  <p>Card body content</p>
</Card>

<Dialog>
  <DialogTrigger><Button>Open</Button></DialogTrigger>
  <DialogSurface>
    <DialogTitle>Title</DialogTitle>
    <DialogContent>Content here</DialogContent>
    <DialogActions>
      <Button appearance="primary">Confirm</Button>
    </DialogActions>
  </DialogSurface>
</Dialog>

<Drawer open={isOpen} onOpenChange={(_, { open }) => setIsOpen(open)}>
  <DrawerHeader><DrawerHeaderTitle>Title</DrawerHeaderTitle></DrawerHeader>
  <DrawerBody>Content</DrawerBody>
</Drawer>
```

### Navigation
```tsx
<Tablist selectedValue={tab} onTabSelect={(_, d) => setTab(d.value)}>
  <Tab value="tab1">Tab 1</Tab>
  <Tab value="tab2">Tab 2</Tab>
</Tablist>

<Menu>
  <MenuTrigger><Button>Menu</Button></MenuTrigger>
  <MenuPopover>
    <MenuList>
      <MenuItem>Item 1</MenuItem>
      <MenuItem>Item 2</MenuItem>
      <MenuDivider />
      <MenuItem>Item 3</MenuItem>
    </MenuList>
  </MenuPopover>
</Menu>

<Breadcrumb>
  <BreadcrumbItem><BreadcrumbButton>Home</BreadcrumbButton></BreadcrumbItem>
  <BreadcrumbDivider />
  <BreadcrumbItem><BreadcrumbButton current>Page</BreadcrumbButton></BreadcrumbItem>
</Breadcrumb>
```

### Feedback & Status
```tsx
<Spinner label="Loading..." />
<ProgressBar value={0.5} />
<Badge appearance="filled">New</Badge>
<MessageBar intent="success">Operation completed</MessageBar>
<Toast>Toast notification</Toast>
<Tooltip content="Helpful tip" relationship="label"><Button>Hover me</Button></Tooltip>
```

### Data Display
```tsx
<Avatar name="John Doe" />
<AvatarGroup><Avatar /><Avatar /></AvatarGroup>
<Persona name="Jane Smith" secondaryText="Designer" avatar={{ color: "colorful" }} />
<Tag>Category</Tag>

<Tree>
  <TreeItem itemType="branch">
    <TreeItemLayout>Parent</TreeItemLayout>
    <Tree><TreeItem itemType="leaf"><TreeItemLayout>Child</TreeItemLayout></TreeItem></Tree>
  </TreeItem>
</Tree>

<Accordion>
  <AccordionItem value="1">
    <AccordionHeader>Section 1</AccordionHeader>
    <AccordionPanel>Content 1</AccordionPanel>
  </AccordionItem>
</Accordion>
```

## Styling with Griffel

Use `makeStyles` for custom styles:

```tsx
import { makeStyles, tokens } from "@fluentui/react-components";

const useStyles = makeStyles({
  container: {
    padding: tokens.spacingVerticalM,
    backgroundColor: tokens.colorNeutralBackground1,
    borderRadius: tokens.borderRadiusMedium,
  },
});

function MyComponent() {
  const styles = useStyles();
  return <div className={styles.container}>Content</div>;
}
```

**Common tokens:**
- Spacing: `tokens.spacingVerticalS`, `tokens.spacingHorizontalM`, etc.
- Colors: `tokens.colorNeutralBackground1`, `tokens.colorBrandBackground`, etc.
- Typography: `tokens.fontSizeBase300`, `tokens.fontWeightSemibold`, etc.
- Border: `tokens.borderRadiusMedium`, `tokens.strokeWidthThin`, etc.

## Resources

- [Component Storybook](https://react.fluentui.dev/)
- [Fluent 2 Design](https://fluent2.microsoft.design/)
- [GitHub](https://github.com/microsoft/fluentui)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
