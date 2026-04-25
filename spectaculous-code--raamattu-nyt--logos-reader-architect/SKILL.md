---
name: logos-reader-architect
description: | Use when this capability is needed.
metadata:
  author: spectaculous-code
---

# Logos Reader Architect

Design-only architecture skill for future-proof Bible reading systems.

## Constraints

**MUST NOT:**
- Modify existing code
- Refactor components
- Write production implementations
- Change database schemas
- Touch Supabase, RPCs, or Edge Functions

**MUST:**
- Design abstractions
- Define data models and contracts
- Name components clearly
- Explain relationships between parts
- Think ahead to embedding, sharing, and audio playback

## Current Architecture Summary

### Component Hierarchy

```
BibleReader.tsx (23K) — Main chapter reader (orchestrator)
├── useChapterBundle hook — Single RPC for all chapter data
├── ChapterReader.tsx (174 lines) — Pure verse list rendering
│   └── VerseRow.tsx (656 lines) — Single verse with outlook modes
├── useBibleReaderAudio — Chapter-level audio control
├── Swipe navigation + keyboard shortcuts
└── Activity logging + listening position save

VerseSetReader.tsx (393 lines) — Arbitrary verse collections
├── Accepts string refs: "Joh.3:16; Matt.5:3-12; Rom.8:28"
├── Multi-segment audio via usePlaylistAudio
├── All 5 outlook templates supported
└── Reuses ChapterReader + VerseRow for display

CinemaReaderScreen.tsx (1,704 lines) — Cinema mode adapter
├── Discriminated union modes: chapter | verseList | summaryItems
├── @raamattu-nyt/cinema-reader package (GSAP presentation)
├── useCinemaAudio — Dual-track (Bible voice + background music)
├── Discipleship task integration (VerseBar, InlineTask, TaskSelector)
└── Ken Burns visual effects + transition overlays
```

### 5-Tier Outlook System

| Tier | Breakpoint | Font | Verse Numbers | Use Case |
|------|-----------|------|---------------|----------|
| studio | ≥1440px | 3xl | badge-xl | Ultra-wide/accessibility |
| classic | 1024–1439px | base | badge | Desktop |
| compact | 768–1023px | base | superscript | Tablet landscape |
| cozy | 480–767px | base | superscript | Tablet portrait/large phone |
| minimal | <480px | sm | inline-subtle | Small phones |

Primary reference: [Docs/context/reader-templates.md](../../../Docs/context/reader-templates.md)

### Audio Systems

| System | Scope | Hook | Key Feature |
|--------|-------|------|-------------|
| Chapter audio | Single chapter | `useBibleReaderAudio` | Cues from `get_chapter_bundle` |
| Playlist audio | Arbitrary verse sets | `usePlaylistAudio` | Multi-range `AudioSegment[]` |
| Cinema audio | Cinema mode | `useCinemaAudio` | Dual-track, estimated timing from word count |

Audio is **no longer chapter-bound** — VerseSetReader and Cinema both support arbitrary verse set playback.

## Design Tasks

Execute these in order when designing a Bible reader architecture:

### 1. Canonical Data Model

Design first-class types for:
- Verse reference (single verse, range)
- Ordered verse sets
- Optional labels/metadata
- Version handling (finstlk201, KJV, etc.)

```typescript
type VerseRef = { book: string; chapter: number; verse: number }
type VerseRange = { start: VerseRef; end: VerseRef }
type VerseSet = { refs: (VerseRef | VerseRange)[]; version: string; label?: string }
```

### 2. Reader Component Taxonomy

Design hierarchy with these established components:
- `VerseRow` — single verse display (all outlooks)
- `ChapterReader` — contiguous verse list (pure, no fetching)
- `VerseSetReader` — arbitrary multi-reference collections with audio
- `BibleReader` — full chapter orchestrator (navigation, audio, state)
- `CinemaReaderScreen` — fullscreen mode adapter with discipleship tasks

Clarify: compositional vs specialized boundaries, data flow direction.

### 3. Outlook Model (5-Tier Template System)

Read `Docs/context/reader-templates.md` for complete specs including:
- Template hierarchy and breakpoints
- Visual differences (verse numbers, spacing, padding, font, actions)
- OutlookConfig type definition and OUTLOOK_PRESETS
- CSS mapping and key implementation files
- Resolution logic with maxOutlook ceiling

Design principles:
- Independent of reader type
- Consistent across app, embed, widget, cinema
- Changes: layout, spacing, actions
- Preserves: semantics, verse identity

### 4. Audio Playback Contract

Three established audio patterns to design around:

```typescript
// Chapter audio — cues from RPC
interface BundleAudio {
  audio_id: string; file_url: string; duration_ms: number;
  cues: { verse_number: number; start_ms: number; end_ms: number }[];
}

// Playlist audio — multi-segment for verse sets
interface AudioSegment {
  rangeIndex: number; file_url: string;
  startTime: number; endTime: number;
}

// Cinema audio — dual-track with optional timing estimation
interface CinemaAudioConfig {
  bibleVoice: { url: string; cues: VerseCue[] };
  backgroundMusic?: { url: string; volume: number };
  estimateTimingFromWordCount?: boolean;
}
```

### 5. Cinema Mode Architecture

Discriminated union for cinema modes:
- `chapter` — sequential chapter reading
- `verseList` — arbitrary verse set presentation
- `summaryItems` — summary-driven content

Design discipleship integration points:
- Task selector overlay before reading starts
- Inline task display during verse transitions
- Verse bar actions when paused
- Pomodoro break integration

### 6. Linking & Embedding Model

Design:
- URL encoding for verse sets
- Embed data delivery mechanism (Edge Function)
- App vs external site differences
- Widget.js Shadow DOM isolation

## Output Format

Produce:
1. **Conceptual overview** — Architecture summary
2. **Type definitions** — TypeScript-style
3. **Component diagram** — ASCII relationships
4. **Design rationale** — Why this scales
5. **Deferred items** — What's intentionally not designed

## Review Workflow

When reviewing a design document, identify issues and present them as **actionable questions with options**.

### Question Format

For each issue, use AskUserQuestion with 2–4 concrete options:

```
Issue: Audio segments keyed by rangeIndex break if ranges reorder.

Question: "How should audio segments be keyed?"
Options:
- "By book.chapter identity (Recommended)" — Survives reordering
- "Keep rangeIndex" — Simpler, reordering not expected
- "Defer decision" — Mark as TODO
```

### Review Categories

Group issues into:
- **Architecture Decisions** — Coupling, extensibility, component boundaries
- **API Surface** — What's exposed, what's internal, response shapes
- **Edge Cases** — Errors, empty states, limits, overrides
- **Missing States** — Loading, error, empty handling

## Network Optimization Analysis

When analyzing duplicate requests or designing lean data fetching:

1. **Capture network log** — Filter by domain, sort by timestamp
2. **Identify duplicates** — Same endpoint multiple times
3. **Trace call sites** — Find all callers
4. **Apply fix patterns** — Lift state up, remove nested fetches, consolidate providers

For detailed patterns and checklist, see [references/network-optimization.md](references/network-optimization.md).

### Quick Diagnostic Questions

- Is the same data fetched by multiple hooks?
- Does a service function fetch data that React Query also manages?
- Are there provider wrappers whose context is never consumed?
- Do child components fetch data they already receive as props?

## Context Files

For project context, read from `Docs/context/`:
- `Docs/context/reader-templates.md` — **5-tier template system** (primary outlook reference)
- `Docs/context/cinema-system.md` — Cinema architecture
- `Docs/context/db-schema-short.md` — Database tables (verses, chapters, audio)
- `Docs/context/supabase-map.md` — Edge Functions (embed, etc.)
- `Docs/context/packages-map.md` — Shared packages (shared-voice, cinema-reader)

For reader-specific patterns, see [references/current-architecture.md](references/current-architecture.md).
For network optimization patterns, see [references/network-optimization.md](references/network-optimization.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spectaculous-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
