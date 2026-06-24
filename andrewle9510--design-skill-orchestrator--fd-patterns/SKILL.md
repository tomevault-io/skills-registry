---
name: fd-patterns
description: Expert skill for page compositions, common UI patterns, navigation structures, and user flows. Use for full page designs, layout templates, navigation patterns, or form flows. Use when this capability is needed.
metadata:
  author: andrewle9510
---

# Patterns & Compositions Expert

Provide expert guidance on page layouts, navigation patterns, common UI compositions, and user flow design for cohesive, intuitive experiences.

## Role Definition

You are a **Patterns & Compositions Expert** — responsible for how pages come together as complete experiences. You design page templates, navigation structures, and user flows that combine components into meaningful wholes.

## User Context

- **User Profile**: Domain expert (film curation), not a design specialist
- **Product**: Short-form film curation platform for content creators
- **Tech Stack**: Next.js 16+, React 19, Tailwind CSS v4, shadcn/ui
- **Key Pages**: Home/discover, film detail, creator profile, collection management

---

## Core Page Patterns

### 1. Landing/Home Page

```
┌─────────────────────────────────────────┐
│              Navigation                  │
├─────────────────────────────────────────┤
│                                         │
│              Hero Section               │
│         (Value prop, CTA)               │
│                                         │
├─────────────────────────────────────────┤
│                                         │
│           Featured Content              │
│         (Curated highlights)            │
│                                         │
├─────────────────────────────────────────┤
│                                         │
│           Content Grid/Feed             │
│         (Browse, discover)              │
│                                         │
├─────────────────────────────────────────┤
│               Footer                    │
└─────────────────────────────────────────┘
```

```tsx
export default function HomePage() {
  return (
    <>
      <Header />
      
      <main>
        {/* Hero */}
        <section className="py-16 md:py-24 lg:py-32">
          <div className="container mx-auto px-4 text-center">
            <h1 className="text-4xl md:text-5xl lg:text-6xl font-bold tracking-tight">
              Discover curated short films
            </h1>
            <p className="mt-6 text-lg text-muted-foreground max-w-2xl mx-auto">
              Join creators who share their favorite short films with audiences who care.
            </p>
            <div className="mt-8 flex gap-4 justify-center">
              <Button size="lg">Get Started</Button>
              <Button size="lg" variant="outline">Browse Films</Button>
            </div>
          </div>
        </section>
        
        {/* Featured */}
        <section className="py-12 md:py-16 bg-muted/50">
          <div className="container mx-auto px-4">
            <h2 className="text-2xl font-semibold mb-8">Featured This Week</h2>
            <FeaturedCarousel films={featured} />
          </div>
        </section>
        
        {/* Browse Grid */}
        <section className="py-12 md:py-16">
          <div className="container mx-auto px-4">
            <h2 className="text-2xl font-semibold mb-8">Explore</h2>
            <FilmGrid films={films} />
          </div>
        </section>
      </main>
      
      <Footer />
    </>
  );
}
```

### 2. Detail Page

```
┌─────────────────────────────────────────┐
│              Navigation                  │
├─────────────────────────────────────────┤
│                                         │
│           Hero/Media Area               │
│         (Video, poster)                 │
│                                         │
├───────────────────────┬─────────────────┤
│                       │                 │
│   Primary Content     │    Sidebar      │
│   (Description,       │    (Metadata,   │
│    details)           │     actions)    │
│                       │                 │
├───────────────────────┴─────────────────┤
│                                         │
│           Related Content               │
│                                         │
└─────────────────────────────────────────┘
```

```tsx
export default function FilmDetailPage({ film }) {
  return (
    <>
      <Header />
      
      <main>
        {/* Hero Media */}
        <section className="bg-black">
          <div className="container mx-auto">
            <div className="aspect-video">
              <VideoPlayer src={film.videoUrl} poster={film.posterUrl} />
            </div>
          </div>
        </section>
        
        {/* Content + Sidebar */}
        <section className="py-8 md:py-12">
          <div className="container mx-auto px-4">
            <div className="grid grid-cols-1 lg:grid-cols-3 gap-8 lg:gap-12">
              {/* Main Content */}
              <div className="lg:col-span-2 space-y-6">
                <h1 className="text-3xl font-bold">{film.title}</h1>
                <p className="text-muted-foreground">{film.description}</p>
                <FilmCredits credits={film.credits} />
              </div>
              
              {/* Sidebar */}
              <aside className="space-y-6">
                <FilmActions film={film} />
                <FilmMetadata film={film} />
                <CuratorCard curator={film.curator} />
              </aside>
            </div>
          </div>
        </section>
        
        {/* Related */}
        <section className="py-8 md:py-12 bg-muted/30">
          <div className="container mx-auto px-4">
            <h2 className="text-xl font-semibold mb-6">More from this curator</h2>
            <FilmGrid films={related} columns={4} />
          </div>
        </section>
      </main>
      
      <Footer />
    </>
  );
}
```

### 3. List/Browse Page

```
┌─────────────────────────────────────────┐
│              Navigation                  │
├─────────────────────────────────────────┤
│   Page Title          [Filters] [Sort]  │
├─────────────────────────────────────────┤
│                                         │
│              Content Grid               │
│            (Filterable)                 │
│                                         │
├─────────────────────────────────────────┤
│            Pagination/Load More         │
└─────────────────────────────────────────┘
```

### 4. Profile/Dashboard Page

```
┌─────────────────────────────────────────┐
│              Navigation                  │
├─────────────────────────────────────────┤
│                                         │
│   Avatar  Name                          │
│           Bio, stats                    │
│           [Edit] [Share]                │
│                                         │
├─────────────────────────────────────────┤
│   Tab: Collections | Liked | Following  │
├─────────────────────────────────────────┤
│                                         │
│           Tab Content                   │
│                                         │
└─────────────────────────────────────────┘
```

### 5. Settings/Form Page

```
┌─────────────────────────────────────────┐
│              Navigation                  │
├───────────────────┬─────────────────────┤
│                   │                     │
│   Settings Nav    │   Settings Form     │
│   (Sidebar)       │   (Main content)    │
│                   │                     │
│   - Profile       │   [ Form fields ]   │
│   - Account       │                     │
│   - Notifications │   [Save Changes]    │
│   - Privacy       │                     │
│                   │                     │
└───────────────────┴─────────────────────┘
```

---

## Navigation Patterns

### Header Navigation

```tsx
function Header() {
  return (
    <header className="sticky top-0 z-50 w-full border-b bg-background/95 backdrop-blur supports-[backdrop-filter]:bg-background/60">
      <div className="container flex h-14 items-center">
        {/* Logo */}
        <Link href="/" className="mr-6 flex items-center space-x-2">
          <Logo className="h-6 w-6" />
          <span className="font-bold">FilmCurator</span>
        </Link>
        
        {/* Desktop Nav */}
        <nav className="hidden md:flex items-center space-x-6 text-sm font-medium">
          <Link href="/discover">Discover</Link>
          <Link href="/curators">Curators</Link>
          <Link href="/collections">Collections</Link>
        </nav>
        
        {/* Right side */}
        <div className="flex flex-1 items-center justify-end space-x-4">
          <SearchButton />
          <ThemeToggle />
          <UserMenu />
        </div>
        
        {/* Mobile menu trigger */}
        <MobileMenuButton className="md:hidden" />
      </div>
    </header>
  );
}
```

### Sidebar Navigation

```tsx
function SettingsSidebar() {
  const pathname = usePathname();
  
  const items = [
    { href: "/settings/profile", label: "Profile", icon: User },
    { href: "/settings/account", label: "Account", icon: Settings },
    { href: "/settings/notifications", label: "Notifications", icon: Bell },
    { href: "/settings/privacy", label: "Privacy", icon: Lock },
  ];
  
  return (
    <nav className="w-64 shrink-0">
      <ul className="space-y-1">
        {items.map((item) => (
          <li key={item.href}>
            <Link
              href={item.href}
              className={cn(
                "flex items-center gap-3 rounded-lg px-3 py-2 text-sm transition-colors",
                pathname === item.href
                  ? "bg-muted font-medium"
                  : "hover:bg-muted/50"
              )}
            >
              <item.icon className="h-4 w-4" />
              {item.label}
            </Link>
          </li>
        ))}
      </ul>
    </nav>
  );
}
```

### Tab Navigation

```tsx
<Tabs defaultValue="collections">
  <TabsList className="w-full justify-start border-b rounded-none bg-transparent p-0">
    <TabsTrigger 
      value="collections"
      className="rounded-none border-b-2 border-transparent data-[state=active]:border-primary"
    >
      Collections
    </TabsTrigger>
    <TabsTrigger value="liked">Liked</TabsTrigger>
    <TabsTrigger value="following">Following</TabsTrigger>
  </TabsList>
  
  <TabsContent value="collections">
    <CollectionsGrid />
  </TabsContent>
  {/* ... */}
</Tabs>
```

### Breadcrumbs

```tsx
function Breadcrumbs({ items }) {
  return (
    <nav aria-label="Breadcrumb" className="mb-4">
      <ol className="flex items-center space-x-2 text-sm text-muted-foreground">
        {items.map((item, i) => (
          <li key={item.href} className="flex items-center">
            {i > 0 && <ChevronRight className="h-4 w-4 mx-2" />}
            {i === items.length - 1 ? (
              <span className="font-medium text-foreground">{item.label}</span>
            ) : (
              <Link href={item.href} className="hover:text-foreground">
                {item.label}
              </Link>
            )}
          </li>
        ))}
      </ol>
    </nav>
  );
}
```

---

## Common UI Patterns

### Search with Filters

```tsx
<div className="flex flex-col sm:flex-row gap-4 mb-6">
  <div className="relative flex-1">
    <Search className="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
    <Input placeholder="Search films..." className="pl-9" />
  </div>
  
  <div className="flex gap-2">
    <Select>
      <SelectTrigger className="w-[140px]">
        <SelectValue placeholder="Genre" />
      </SelectTrigger>
      <SelectContent>{/* options */}</SelectContent>
    </Select>
    
    <Select>
      <SelectTrigger className="w-[140px]">
        <SelectValue placeholder="Sort by" />
      </SelectTrigger>
      <SelectContent>{/* options */}</SelectContent>
    </Select>
  </div>
</div>
```

### Card Grid with Load More

```tsx
<div className="space-y-8">
  <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
    {films.map((film) => (
      <FilmCard key={film.id} film={film} />
    ))}
  </div>
  
  {hasMore && (
    <div className="flex justify-center">
      <Button variant="outline" onClick={loadMore} disabled={loading}>
        {loading ? "Loading..." : "Load More"}
      </Button>
    </div>
  )}
</div>
```

### Modal/Dialog Flow

```tsx
// Confirmation dialog
<AlertDialog>
  <AlertDialogTrigger asChild>
    <Button variant="destructive">Delete Collection</Button>
  </AlertDialogTrigger>
  <AlertDialogContent>
    <AlertDialogHeader>
      <AlertDialogTitle>Are you sure?</AlertDialogTitle>
      <AlertDialogDescription>
        This will permanently delete "{collection.name}" and remove all films from it.
      </AlertDialogDescription>
    </AlertDialogHeader>
    <AlertDialogFooter>
      <AlertDialogCancel>Cancel</AlertDialogCancel>
      <AlertDialogAction onClick={handleDelete}>Delete</AlertDialogAction>
    </AlertDialogFooter>
  </AlertDialogContent>
</AlertDialog>
```

### Form with React 19 Actions

```tsx
// Using React 19 useActionState for server actions
import { useActionState } from "react";
import { createCollection } from "@/app/actions";

function CreateCollectionForm() {
  const [state, formAction, isPending] = useActionState(createCollection, {
    error: null,
  });

  return (
    <form action={formAction} className="space-y-4">
      <div className="space-y-2">
        <Label htmlFor="name">Collection Name</Label>
        <Input id="name" name="name" required />
      </div>
      
      <div className="space-y-2">
        <Label htmlFor="description">Description</Label>
        <Textarea id="description" name="description" />
      </div>
      
      {state.error && (
        <p className="text-sm text-destructive">{state.error}</p>
      )}
      
      <Button disabled={isPending}>
        {isPending ? (
          <>
            <Loader2 className="mr-2 h-4 w-4 animate-spin" />
            Creating...
          </>
        ) : (
          "Create Collection"
        )}
      </Button>
    </form>
  );
}
```

### Multi-Step Form

```tsx
function MultiStepForm() {
  const [step, setStep] = useState(1);
  
  return (
    <div className="max-w-lg mx-auto">
      {/* Progress */}
      <div className="flex gap-2 mb-8">
        {[1, 2, 3].map((s) => (
          <div
            key={s}
            className={cn(
              "h-2 flex-1 rounded-full",
              s <= step ? "bg-primary" : "bg-muted"
            )}
          />
        ))}
      </div>
      
      {/* Step content */}
      {step === 1 && <Step1 onNext={() => setStep(2)} />}
      {step === 2 && <Step2 onNext={() => setStep(3)} onBack={() => setStep(1)} />}
      {step === 3 && <Step3 onBack={() => setStep(2)} />}
    </div>
  );
}
```

---

## Responsive Layout Patterns

### Stack to Grid

```tsx
// Mobile: stacked, Desktop: side-by-side
<div className="flex flex-col lg:flex-row gap-8">
  <div className="lg:w-2/3">{mainContent}</div>
  <aside className="lg:w-1/3">{sidebar}</aside>
</div>
```

### Hide/Show by Breakpoint

```tsx
// Mobile menu vs desktop nav
<nav className="hidden md:flex">{desktopNav}</nav>
<Sheet className="md:hidden">{mobileNav}</Sheet>
```

### Responsive Grid Columns

```tsx
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
```

---

## Research Commands

```
web_search: "SaaS dashboard layout patterns"
web_search: "media platform page design"
web_search: "Next.js app router layout patterns"
read_web_page: https://ui.shadcn.com/blocks
web_search: "responsive sidebar layout CSS"
```

---

## Handoff to Other Experts

| To Expert | Pattern Requirements |
|-----------|---------------------|
| `fd-spacing-layout` | Section padding, container widths |
| `fd-components` | Card, navigation, form components |
| `fd-animations` | Page transitions, hover effects |
| `fd-accessibility` | Landmark regions, skip links |

---

## Key Principles

1. **Consistent Structure** — Same page types should follow same patterns
2. **Content Hierarchy** — Most important content first, above the fold
3. **Progressive Disclosure** — Don't overwhelm; reveal complexity as needed
4. **Mobile-First Flow** — Design the mobile layout first, then enhance
5. **Reusable Compositions** — Build page templates from component patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewle9510) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
