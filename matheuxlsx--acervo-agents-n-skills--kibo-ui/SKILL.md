---
name: kibo-ui
description: Complete skill for developing, suggesting, and implementing Kibo UI components. Provides comprehensive knowledge about all available components, CLI commands, composition patterns, and best practices for using Kibo UI in React/Next.js projects with enhanced UX and polished aesthetics. Built on top of shadcn/ui for richer, functional components like Kanban, Gantt, and advanced data displays. Use when this capability is needed.
metadata:
  author: matheuxlsx
---

# Kibo UI Development Skill

This skill provides comprehensive knowledge for developing, suggesting, and implementing Kibo UI components in React/Next.js projects. Kibo UI is a component library and custom registry built on top of shadcn/ui, designed to help developers build richer UIs faster with pre-built, composable, and accessible components.

## Overview

### What is Kibo UI?

Kibo UI fills the gap between shadcn/ui primitives and monolithic component libraries:
- **Not just styled clones** - Components come with built-in functionality and logic
- **Real behavior** - AI Chat inputs, Kanban boards, Gantt charts include necessary behavior out of the box
- **Composable blocks** - Each component is designed as a building block
- **Accessible** - Built on Radix UI primitives with full accessibility support
- **Customizable** - Code is added to your project, fully modifiable

### Key Characteristics

| Aspect | Description |
|--------|-------------|
| **Stack** | React, TypeScript, Tailwind CSS, Radix UI |
| **Built On** | shadcn/ui foundations |
| **Installation** | Via Kibo UI CLI or shadcn CLI |
| **Customization** | Full code ownership - edit directly |
| **Open Source** | Free and always will be |

## Installation

### Prerequisites

Before using Kibo UI, ensure you have:
- React 18+
- TypeScript configured
- Tailwind CSS set up
- shadcn/ui (optional but recommended)

### Quick Start

Install Kibo UI components using either CLI:

```bash
# Method 1: Kibo UI CLI (Recommended)
npx kibo-ui add [component-name]

# Method 2: shadcn CLI (If using shadcn/ui)
npx shadcn@latest add [component-name]
```

Both methods achieve the same result:
- Downloads component code to your project
- Installs necessary dependencies automatically
- Places components in `components/kibo-ui/` directory

### Fast Installation

Installation is very fast - typically taking only a few seconds:
- No manual copy-pasting required
- No leaving your editor
- Everything is ready to use immediately after command finishes

## Component Categories Quick Reference

| Category | Components | Use Case |
|----------|-------------|----------|
| **Collaboration** | AvatarStack, Cursor | Real-time collaborative apps |
| **Project Management** | Calendar, Gantt, Kanban, List, Table | Task management, timelines |
| **Code** | CodeBlock, ContributionGraph, Sandbox, Snippet | Code display, GitHub-style graphs |
| **Forms** | ChoiceBox, Combobox, Dropzone, MiniCalendar, Tags | Advanced form inputs |
| **Images** | ImageCrop, ImageZoom | Image manipulation |
| **Finance** | CreditCard, Ticker | Financial dashboards |
| **Social** | Stories, Reel, VideoPlayer | Social media features |
| **Callouts** | Announcement, Banner | Marketing, announcements |
| **Utility** | ColorPicker, Comparison, Deck, DialogStack, Editor, Glimpse, Marquee, Pill, QRCode, Rating, RelativeTime, Spinner, Status, ThemeSwitcher, Tree | Various UI needs |

## Essential Components

### Avatar Stack

Display stacked avatars for teams or participants:

```tsx
import { AvatarStack } from "@/components/kibo-ui/avatar-stack"
import { Avatar, AvatarImage, AvatarFallback } from "@/components/ui/avatar"

<AvatarStack>
  <Avatar>
    <AvatarImage src="/avatar1.png" />
    <AvatarFallback>AB</AvatarFallback>
  </Avatar>
  <Avatar>
    <AvatarImage src="/avatar2.png" />
    <AvatarFallback>CD</AvatarFallback>
  </Avatar>
  <Avatar>
    <AvatarImage src="/avatar3.png" />
    <AvatarFallback>EF</AvatarFallback>
  </Avatar>
</AvatarStack>
```

### Gantt Chart

Interactive timeline with drag & drop functionality:

```tsx
import { Gantt } from "@/components/kibo-ui/gantt"

<Gantt
  items={[
    {
      id: "1",
      title: "Task 1",
      duration: "7 months",
      startDate: new Date("2025-01-01"),
      endDate: new Date("2025-07-01"),
    },
    {
      id: "2",
      title: "Task 2",
      duration: "3 months",
      startDate: new Date("2025-02-15"),
      endDate: new Date("2025-05-15"),
    },
  ]}
  onDateClick={(date) => console.log("Clicked:", date)}
  onItemClick={(item) => console.log("Item:", item)}
/>
```

**Gantt Features:**
- Resizable and draggable timeline
- Date markers for important dates
- "Today" marker
- Feature grouping
- Multiple items per line (for hotels, reservations)
- Read-only version available

### Kanban Board

Drag & drop task management board:

```tsx
import { Kanban, Column, Card } from "@/components/kibo-ui/kanban"

<Kanban>
  <Column id="todo" title="To Do">
    <Card id="task-1">
      <h3>Design Review</h3>
      <p>Review new mockups</p>
    </Card>
    <Card id="task-2">
      <h3>API Integration</h3>
      <p>Connect to backend</p>
    </Card>
  </Column>
  <Column id="in-progress" title="In Progress">
    <Card id="task-3">
      <h3>User Authentication</h3>
      <p>Implement login flow</p>
    </Card>
  </Column>
  <Column id="done" title="Done">
    <Card id="task-4">
      <h3>Setup Project</h3>
      <p>Initial configuration</p>
    </Card>
  </Column>
</Kanban>
```

**Kanban Features:**
- Drag & drop between columns
- Customizable card content
- Multiple column support
- Keyboard accessible

### Calendar

Full-featured calendar component:

```tsx
import { Calendar } from "@/components/kibo-ui/calendar"

<Calendar
  mode="single"
  selected={date}
  onSelect={setDate}
  disabled={(date) => date < new Date()}
  className="rounded-md border"
/>
```

### Dropzone

Drag & drop file upload area:

```tsx
import { Dropzone } from "@/components/kibo-ui/dropzone"

<Dropzone
  accept={{
    "image/*": [".png", ".jpg", ".jpeg"],
    "application/pdf": [".pdf"],
  }}
  maxSize={10 * 1024 * 1024} // 10MB
  onDrop={(files) => {
    console.log("Uploaded files:", files)
    // Handle file upload
  }}
>
  <div className="text-center">
    <p className="font-medium">
      Drag and drop files here or click to browse
    </p>
    <p className="text-sm text-muted-foreground">
      Accepts images and PDF up to 10MB
    </p>
  </div>
</Dropzone>
```

**Dropzone Features:**
- File type validation
- Size limits
- Drag & drop support
- Click to upload alternative
- File preview

### Combobox

Advanced autocomplete with search:

```tsx
import { Combobox } from "@/components/kibo-ui/combobox"

<Combobox
  options={[
    { value: "react", label: "React" },
    { value: "vue", label: "Vue" },
    { value: "angular", label: "Angular" },
    { value: "svelte", label: "Svelte" },
  ]}
  value={framework}
  onChange={setFramework}
  placeholder="Select framework..."
  searchPlaceholder="Search frameworks..."
/>
```

### Code Block

Syntax-highlighted code display:

```tsx
import { CodeBlock } from "@/components/kibo-ui/code-block"

<CodeBlock
  language="typescript"
  code={`function greet(name: string): string {
  return \`Hello, \${name}!\`;
}

console.log(greet("World"));`}
  showLineNumbers
  showCopyButton
  maxHeight="400px"
/>
```

### Credit Card

Payment form component:

```tsx
import { CreditCard } from "@/components/kibo-ui/credit-card"

<CreditCard
  number="4242 4242 4242 4242"
  expiry="12/28"
  name="JOHN DOE"
  brand="visa"
  className="w-full max-w-md"
/>
```

### Announcement

Callout for announcements and updates:

```tsx
import {
  Announcement,
  AnnouncementTag,
  AnnouncementTitle,
} from "@/components/kibo-ui/announcement"

<Announcement>
  <AnnouncementTag>New Feature</AnnouncementTag>
  <AnnouncementTitle>
    AI-Powered Analytics Now Available
    <ArrowUpRightIcon size={16} />
  </AnnouncementTitle>
  <p className="text-sm">
    Get insights faster with our new AI analytics dashboard.
  </p>
</Announcement>
```

### Video Player

Customizable video player:

```tsx
import { VideoPlayer } from "@/components/kibo-ui/video-player"

<VideoPlayer
  src="/videos/demo.mp4"
  poster="/images/video-poster.jpg"
  controls
  autoplay={false}
  loop={false}
  muted={false}
  className="w-full rounded-lg overflow-hidden"
/>
```

### Theme Switcher

Dark/light mode toggle:

```tsx
import { ThemeSwitcher } from "@/components/kibo-ui/theme-switcher"

<ThemeSwitcher
  variant="dropdown" // or "toggle", "buttons"
  showLabels
  defaultTheme="system"
/>
```

### QR Code

Generate QR codes:

```tsx
import { QRCode } from "@/components/kibo-ui/qr-code"

<QRCode
  value="https://example.com"
  size={200}
  level="H" // L, M, Q, H
  includeMargin
  imageSettings={{
    src: "/logo.png",
    height: 24,
    width: 24,
    excavate: true,
  }}
/>
```

### Color Picker

Figma-like color picker:

```tsx
import { ColorPicker } from "@/components/kibo-ui/color-picker"

<ColorPicker
  value={color}
  onChange={setColor}
  format="hex" // hex, rgb, hsl
  showInput
  showPicker
  showAlpha
/>
```

### Tags Input

Tag selection component:

```tsx
import { Tags } from "@/components/kibo-ui/tags"

<Tags
  value={tags}
  onChange={setTags}
  placeholder="Add tags..."
  maxTags={5}
  allowDuplicates={false}
  onTagAdd={(tag) => console.log("Added:", tag)}
  onTagRemove={(tag) => console.log("Removed:", tag)}
/>
```

## Advanced Patterns

### Kanban + Gantt Integration

Combine for comprehensive project management:

```tsx
<div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
  <div className="space-y-4">
    <h2 className="text-lg font-semibold">Kanban Board</h2>
    <Kanban data={kanbanData} />
  </div>
  <div className="space-y-4">
    <h2 className="text-lg font-semibold">Timeline</h2>
    <Gantt items={ganttItems} />
  </div>
</div>
```

### Form with Dropzone

Rich form with file upload:

```tsx
import { Combobox } from "@/components/kibo-ui/combobox"
import { Dropzone } from "@/components/kibo-ui/dropzone"
import { Tags } from "@/components/kibo-ui/tags"
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card"

<Card className="w-full max-w-lg">
  <CardHeader>
    <CardTitle>Submit Your Project</CardTitle>
  </CardHeader>
  <CardContent className="space-y-4">
    <Combobox
      options={categories}
      value={category}
      onChange={setCategory}
      placeholder="Select category..."
    />
    <Dropzone
      accept={{ "application/pdf": [".pdf"] }}
      maxSize={5 * 1024 * 1024}
      onDrop={handleFileDrop}
    />
    <Tags
      value={tags}
      onChange={setTags}
      placeholder="Add relevant tags..."
    />
    <div className="flex justify-end gap-2">
      <Button variant="outline">Cancel</Button>
      <Button onClick={handleSubmit}>Submit Project</Button>
    </div>
  </CardContent>
</Card>
```

### Collaborative Dashboard

Real-time collaboration UI:

```tsx
import { AvatarStack } from "@/components/kibo-ui/avatar-stack"
import { Cursor } from "@/components/kibo-ui/cursor"
import { RelativeTime } from "@/components/kibo-ui/relative-time"

<div className="relative">
  {/* Active collaborators */}
  <div className="absolute top-4 right-4 z-10">
    <AvatarStack>
      {collaborators.map((user) => (
        <Avatar key={user.id}>
          <AvatarImage src={user.avatar} />
          <AvatarFallback>{user.initials}</AvatarFallback>
        </Avatar>
      ))}
    </AvatarStack>
  </div>

  {/* Collaborative cursor */}
  {activeCursors.map((cursor) => (
    <Cursor
      key={cursor.id}
      name={cursor.name}
      color={cursor.color}
      position={cursor.position}
    />
  ))}

  {/* Main content */}
  <div className="pt-12">
    {/* Dashboard content */}
  </div>
</div>
```

### Code Showcase

Documentation-style code display:

```tsx
import { CodeBlock } from "@/components/kibo-ui/code-block"
import { CopyButton } from "@/components/kibo-ui/copy-button"

<div className="space-y-4">
  <div className="flex items-center justify-between">
    <h3 className="text-lg font-medium">Installation</h3>
    <CopyButton value="npm install @kibo-ui/core" />
  </div>
  <CodeBlock
    language="bash"
    code="npm install @kibo-ui/core"
    showLineNumbers
  />
</div>
```

## CLI Commands

### Installing Components

```bash
# Install a specific component
npx kibo-ui add gantt

# Install multiple components
npx kibo-ui add kanban calendar table

# Using shadcn CLI
npx shadcn@latest add gantt
```

### Component Updates

```bash
# Reinstall to get latest version
npx kibo-ui add gantt --overwrite

# Or with shadcn CLI
npx shadcn@latest add gantt --overwrite
```

## Dependencies Reference

Some components require external libraries:

| Component | Dependencies |
|-----------|-------------|
| Gantt | `@dnd-kit/core`, `@dnd-kit/utilities`, `date-fns`, `lodash` |
| Kanban | `@dnd-kit/core`, `@dnd-kit/sortable`, `usehooks-ts` |
| Dropzone | `react-dropzone` |
| Image Crop | `react-image-crop` |
| Code Block | `prismjs`, `clipboard` |
| QR Code | `qrcode` |
| Color Picker | `react-colorful` |

Dependencies are installed automatically when adding components.

## Best Practices

### 1. Composability

Kibo UI components are designed to work together:
- Combine Kibo UI with shadcn/ui primitives
- Use sub-components for customization
- Nest components as needed

### 2. Customization

Since code lives in your project:
- Modify components directly
- Maintain accessibility patterns
- Follow shadcn/ui styling conventions

### 3. TypeScript

Leverage full type safety:
- Complete IntelliSense available
- Use exported component types
- Well-documented props with comments

### 4. Performance

Optimize for complex components:
- Use React.lazy() for heavy components
- Consider tree-shaking
- Apply React.memo() to complex components

### 5. Accessibility

Built on Radix UI foundations:
- ARIA attributes included
- Full keyboard navigation
- Screen reader support

## Kibo UI vs shadcn/ui

| Aspect | shadcn/ui | Kibo UI |
|--------|-----------|---------|
| **Focus** | Primitive, accessible building blocks | Complex, feature-rich components |
| **Complexity** | Lower - basic components | Higher - pre-built functionality |
| **Customization** | Full - edit everything | Full - everything is editable |
| **Best For** | Forms, buttons, dialogs | Kanban, Gantt, dashboards |
| **Learning Curve** | Lower | Medium |

**When to use Kibo UI:**
- Need Kanban boards
- Building project timelines (Gantt)
- Complex data tables
- File upload interfaces
- Real-time collaboration features
- Rich form experiences

**When to stick with shadcn/ui:**
- Simple forms
- Basic navigation
- Standard dialogs/modals
- Display components
- When you need maximum control

## MCP Server Integration

Kibo UI provides an MCP (Model Context Protocol) server for AI assistants:

```bash
# Install MCP server
npm install -D @kibo-ui/mcp

# Configure in your MCP config
```

Test integration by asking your AI:
> "What Kibo UI components are available for building a dashboard?"

## Resources

| Resource | URL |
|----------|-----|
| Official Website | https://www.kibo-ui.com |
| Documentation | https://www.kibo-ui.com/docs |
| Components | https://www.kibo-ui.com/components |
| Blocks | https://www.kibo-ui.com/blocks |
| Patterns | https://www.kibo-ui.com/patterns |
| GitHub | https://github.com/shadcnblocks/kibo |

## When to Use This Skill

Use this skill when the user:

- Needs project management components (Kanban, Gantt, Calendar)
- Wants collaborative features (AvatarStack, Cursor)
- Requires advanced forms (Dropzone, Combobox, Tags)
- Needs code display (CodeBlock, ContributionGraph)
- Wants financial components (CreditCard, Ticker)
- Asks for pre-built blocks (Form, Roadmap, Pricing)
- Needs rich media (VideoPlayer, Stories)
- Wants enhanced UI elements (ColorPicker, ThemeSwitcher, QRCode)

## Quick Component Reference

```
Collaboration:     AvatarStack, Cursor
Project Mgmt:      Calendar, Gantt, Kanban, List, Table
Code Display:      CodeBlock, ContributionGraph, Sandbox, Snippet
Forms:             ChoiceBox, Combobox, Dropzone, MiniCalendar, Tags
Images:            ImageCrop, ImageZoom
Finance:           CreditCard, Ticker
Social:            Stories, Reel, VideoPlayer
Callouts:          Announcement, Banner
Utility:           ColorPicker, Comparison, Deck, DialogStack, Editor,
                   Glimpse, Marquee, Pill, QRCode, Rating, RelativeTime,
                   Spinner, Status, ThemeSwitcher, Tree
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matheuxlsx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
