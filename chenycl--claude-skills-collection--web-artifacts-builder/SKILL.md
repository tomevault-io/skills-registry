---
name: web-artifacts-builder
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Web Artifacts Builder

Create elaborate, multi-component HTML artifacts using React, Tailwind CSS, and shadcn/ui.

## Available Libraries

```javascript
// React (built-in)
import React, { useState, useEffect, useMemo } from 'react';

// Tailwind CSS (built-in classes)
// Use utility classes directly

// shadcn/ui components
import { Button } from '@/components/ui/button';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Tabs, TabsList, TabsTrigger, TabsContent } from '@/components/ui/tabs';
import { Alert, AlertDescription } from '@/components/ui/alert';
import { Badge } from '@/components/ui/badge';
import { Dialog, DialogTrigger, DialogContent } from '@/components/ui/dialog';

// Lucide icons
import { Search, Plus, Settings, ChevronRight } from 'lucide-react';

// Recharts
import { LineChart, Line, XAxis, YAxis, Tooltip, ResponsiveContainer } from 'recharts';

// Other available libraries
import * as d3 from 'd3';
import _ from 'lodash';
import * as math from 'mathjs';
```

## Component Templates

### Basic Card Component
```jsx
export default function MyCard() {
  return (
    <Card className="w-full max-w-md">
      <CardHeader>
        <CardTitle>Card Title</CardTitle>
      </CardHeader>
      <CardContent>
        <p className="text-gray-600">Card content goes here.</p>
      </CardContent>
    </Card>
  );
}
```

### Interactive Form
```jsx
export default function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted:', formData);
  };

  return (
    <Card className="w-full max-w-md mx-auto">
      <CardHeader>
        <CardTitle>Contact Us</CardTitle>
      </CardHeader>
      <CardContent>
        <form onSubmit={handleSubmit} className="space-y-4">
          <div className="space-y-2">
            <Label htmlFor="name">Name</Label>
            <Input
              id="name"
              value={formData.name}
              onChange={(e) => setFormData({...formData, name: e.target.value})}
              placeholder="Your name"
            />
          </div>
          <div className="space-y-2">
            <Label htmlFor="email">Email</Label>
            <Input
              id="email"
              type="email"
              value={formData.email}
              onChange={(e) => setFormData({...formData, email: e.target.value})}
              placeholder="your@email.com"
            />
          </div>
          <Button type="submit" className="w-full">
            Send Message
          </Button>
        </form>
      </CardContent>
    </Card>
  );
}
```

### Dashboard with Charts
```jsx
export default function Dashboard() {
  const data = [
    { name: 'Jan', value: 400 },
    { name: 'Feb', value: 300 },
    { name: 'Mar', value: 600 },
    { name: 'Apr', value: 800 },
    { name: 'May', value: 500 }
  ];

  return (
    <div className="p-6 space-y-6">
      <h1 className="text-2xl font-bold">Dashboard</h1>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
        <Card>
          <CardHeader>
            <CardTitle className="text-sm text-gray-500">Total Users</CardTitle>
          </CardHeader>
          <CardContent>
            <p className="text-3xl font-bold">12,345</p>
            <Badge className="mt-2" variant="secondary">+12%</Badge>
          </CardContent>
        </Card>

        <Card>
          <CardHeader>
            <CardTitle className="text-sm text-gray-500">Revenue</CardTitle>
          </CardHeader>
          <CardContent>
            <p className="text-3xl font-bold">$45,678</p>
            <Badge className="mt-2" variant="secondary">+8%</Badge>
          </CardContent>
        </Card>

        <Card>
          <CardHeader>
            <CardTitle className="text-sm text-gray-500">Active Now</CardTitle>
          </CardHeader>
          <CardContent>
            <p className="text-3xl font-bold">573</p>
            <Badge className="mt-2" variant="destructive">-3%</Badge>
          </CardContent>
        </Card>
      </div>

      <Card>
        <CardHeader>
          <CardTitle>Monthly Trend</CardTitle>
        </CardHeader>
        <CardContent>
          <ResponsiveContainer width="100%" height={300}>
            <LineChart data={data}>
              <XAxis dataKey="name" />
              <YAxis />
              <Tooltip />
              <Line type="monotone" dataKey="value" stroke="#8884d8" strokeWidth={2} />
            </LineChart>
          </ResponsiveContainer>
        </CardContent>
      </Card>
    </div>
  );
}
```

### Tabbed Interface
```jsx
export default function TabbedContent() {
  return (
    <Tabs defaultValue="overview" className="w-full max-w-2xl">
      <TabsList className="grid w-full grid-cols-3">
        <TabsTrigger value="overview">Overview</TabsTrigger>
        <TabsTrigger value="analytics">Analytics</TabsTrigger>
        <TabsTrigger value="settings">Settings</TabsTrigger>
      </TabsList>
      <TabsContent value="overview">
        <Card>
          <CardContent className="pt-6">
            <p>Overview content here...</p>
          </CardContent>
        </Card>
      </TabsContent>
      <TabsContent value="analytics">
        <Card>
          <CardContent className="pt-6">
            <p>Analytics content here...</p>
          </CardContent>
        </Card>
      </TabsContent>
      <TabsContent value="settings">
        <Card>
          <CardContent className="pt-6">
            <p>Settings content here...</p>
          </CardContent>
        </Card>
      </TabsContent>
    </Tabs>
  );
}
```

## Tailwind Quick Reference

### Layout
```jsx
// Flexbox
<div className="flex items-center justify-between gap-4">

// Grid
<div className="grid grid-cols-3 gap-4">

// Container
<div className="container mx-auto px-4">
```

### Spacing
```jsx
// Padding
<div className="p-4 px-6 py-2">

// Margin
<div className="m-4 mx-auto my-2">

// Gap (flex/grid)
<div className="gap-4 gap-x-2 gap-y-6">
```

### Typography
```jsx
<h1 className="text-4xl font-bold text-gray-900">
<p className="text-sm text-gray-600 leading-relaxed">
<span className="font-medium text-blue-600">
```

### Colors
```jsx
// Background
<div className="bg-white bg-gray-100 bg-blue-500">

// Text
<p className="text-gray-900 text-blue-600">

// Border
<div className="border border-gray-200 border-blue-500">
```

### Responsive
```jsx
<div className="
  w-full           // mobile
  md:w-1/2         // tablet
  lg:w-1/3         // desktop
">
```

## Best Practices

### State Management
```jsx
// Use useState for component state
const [isOpen, setIsOpen] = useState(false);

// Use useMemo for expensive calculations
const sortedData = useMemo(() => {
  return data.sort((a, b) => a.value - b.value);
}, [data]);

// Use useEffect for side effects
useEffect(() => {
  // Fetch data, set up subscriptions, etc.
}, [dependency]);
```

### Component Structure
```jsx
// Keep components focused
// Split large components into smaller ones
// Use composition over props drilling

function ParentComponent() {
  const [data, setData] = useState([]);

  return (
    <div>
      <Header />
      <DataList data={data} />
      <Footer />
    </div>
  );
}
```

### Accessibility
```jsx
// Use semantic HTML
<button> not <div onClick>

// Add aria labels
<button aria-label="Close dialog">

// Ensure keyboard navigation
<button onKeyDown={handleKeyDown}>
```

## Common Patterns

### Loading State
```jsx
const [loading, setLoading] = useState(true);

if (loading) {
  return <div className="animate-pulse">Loading...</div>;
}
```

### Error Handling
```jsx
const [error, setError] = useState(null);

if (error) {
  return (
    <Alert variant="destructive">
      <AlertDescription>{error}</AlertDescription>
    </Alert>
  );
}
```

### Empty State
```jsx
if (items.length === 0) {
  return (
    <div className="text-center py-12">
      <p className="text-gray-500">No items found</p>
      <Button className="mt-4">Add Item</Button>
    </div>
  );
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
