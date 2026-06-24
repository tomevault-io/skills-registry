---
name: swimlane-visualization
description: Design swimlane UI patterns for visualizing ADW execution. Use when building observability dashboards, monitoring agent workflows, or creating real-time status displays. Use when this capability is needed.
metadata:
  author: melodic-software
---

# Swimlane Visualization

## MANDATORY: docs-management Delegation

> **Documentation Verification:** This skill references Claude Code internal types (event types, hook events) that may change between releases. Before relying on hardcoded values, invoke the `hook-management` skill which delegates to `docs-management` for authoritative documentation.

**What's Static (design decisions):**

- UI patterns (lane layout, card design, status indicators)
- Component specifications (Vue/React templates)
- WebSocket integration patterns

**What's Dynamic (verify via docs-management):**

- Event type names (PreToolUse, PostToolUse, TextBlock, etc.)
- Hook event payloads and structure
- Claude Code internal APIs

---

Design swimlane UI patterns for visualizing AI Developer Workflow execution in real-time.

## When to Use

- Building ADW observability dashboards
- Monitoring agent workflow progress
- Creating real-time status displays
- Designing event inspection interfaces
- Implementing workflow debugging tools

## Prerequisites

- Understanding of @websocket-architecture.md for real-time updates
- Familiarity with @hook-event-patterns.md for event types
- Frontend development capabilities (Vue, React, etc.)

## SDK Requirement

> **Implementation Note**: Swimlane visualization requires frontend development. This skill provides UI specifications and component patterns.

## Swimlane Layout

### Basic Structure

```text
┌─────────────────────────────────────────────────────────────────┐
│ ADW: a1b2c3d4 - Feature: Add rate limiting                      │
├────────────┬────────────┬────────────┬────────────┬─────────────┤
│    Plan    │   Build    │   Review   │    Fix     │   Status    │
├────────────┼────────────┼────────────┼────────────┼─────────────┤
│ ✅ Complete │ ⚡ Active  │ ○ Pending  │ ○ Pending  │ ● Running   │
│            │            │            │            │             │
│ 💬 Created │ 🛠️ Write   │            │            │ Cost: $0.42 │
│    spec    │   auth.py  │            │            │             │
│            │ 🛠️ Write   │            │            │ Tokens: 12K │
│            │   tests.py │            │            │             │
│            │ 🧠 Thinking│            │            │ Duration:   │
│            │   ...      │            │            │   45s       │
└────────────┴────────────┴────────────┴────────────┴─────────────┘
```

### Lane States

| State | Icon | Color | Meaning |
| --- | --- | --- | --- |
| Pending | ○ | Gray | Not started |
| Active | ⚡ | Blue | Currently executing |
| Complete | ✅ | Green | Successfully finished |
| Failed | ❌ | Red | Error occurred |

## Event Card Design

### Event Card Structure

```text
┌─────────────────────────────────┐
│ 🛠️ ToolUseBlock                 │
├─────────────────────────────────┤
│ Write: src/auth/middleware.py   │
│                                 │
│ "Implementing JWT validation    │
│  middleware for API routes"     │
├─────────────────────────────────┤
│ 10:32:15 | 45ms                 │
└─────────────────────────────────┘
```

### Event Type Icons

> **Documentation Verification:** Event types (TextBlock, ToolUseBlock, PreToolUse, PostToolUse, etc.) are Claude Code internal types. For authoritative current types, verify via `hook-management` skill → `docs-management`.

| Event Type | Icon | Card Style |
| --- | --- | --- |
| TextBlock | 💬 | Blue border |
| ToolUseBlock | 🛠️ | Orange border |
| ThinkingBlock | 🧠 | Purple border |
| PreToolUse | 🪝 | Light gray |
| PostToolUse | 🪝 | Light gray |
| StepStart | ⚙️ | Green accent |
| StepEnd | ⚙️ | Green accent |

## Component Specifications

### Lane Component

```vue
<template>
  <div class="lane" :class="{ active: isActive }">
    <div class="lane-header">
      <span class="lane-icon">{{ stateIcon }}</span>
      <span class="lane-name">{{ step.name }}</span>
    </div>
    <div class="lane-content">
      <EventCard
        v-for="event in events"
        :key="event.id"
        :event="event"
        @click="$emit('select', event)"
      />
    </div>
  </div>
</template>
```

### Event Card Component

```vue
<template>
  <div class="event-card" :class="eventTypeClass">
    <div class="event-header">
      <span class="event-icon">{{ typeIcon }}</span>
      <span class="event-type">{{ event.event_type }}</span>
    </div>
    <div class="event-body">
      <p class="event-summary">{{ event.summary }}</p>
    </div>
    <div class="event-footer">
      <span class="event-time">{{ formattedTime }}</span>
      <span class="event-duration" v-if="event.duration_ms">
        {{ event.duration_ms }}ms
      </span>
    </div>
  </div>
</template>
```

### Status Panel Component

```vue
<template>
  <div class="status-panel">
    <div class="status-indicator" :class="overallStatus">
      <span class="status-dot"></span>
      <span class="status-text">{{ statusText }}</span>
    </div>
    <div class="metrics">
      <div class="metric">
        <span class="metric-label">Cost</span>
        <span class="metric-value">${{ totalCost.toFixed(2) }}</span>
      </div>
      <div class="metric">
        <span class="metric-label">Tokens</span>
        <span class="metric-value">{{ formatTokens(totalTokens) }}</span>
      </div>
      <div class="metric">
        <span class="metric-label">Duration</span>
        <span class="metric-value">{{ formatDuration(totalDuration) }}</span>
      </div>
    </div>
  </div>
</template>
```

## Real-Time Update Pattern

### WebSocket Integration

```typescript
class SwimlaneLive {
  private ws: ADWWebSocket;
  private lanes: Map<string, Lane> = new Map();

  connect(adwId: string) {
    this.ws = new ADWWebSocket();
    this.ws.connect();
    this.ws.subscribe(adwId, this.handleEvent.bind(this));
  }

  handleEvent(event: ADWEvent) {
    const lane = this.lanes.get(event.step);
    if (lane) {
      lane.addEvent(event);
      this.updateLaneState(event.step);
    }
  }

  updateLaneState(step: string) {
    // Update lane visual state based on latest event
  }
}
```

### Event Buffering

```typescript
class EventBuffer {
  private buffer: ADWEvent[] = [];
  private flushInterval = 100; // ms

  add(event: ADWEvent) {
    this.buffer.push(event);
  }

  flush(): ADWEvent[] {
    const events = [...this.buffer];
    this.buffer = [];
    return events;
  }
}
```

## Filtering and Navigation

### Filter Controls

```text
┌─────────────────────────────────────────────────────────────────┐
│ Filters:                                                         │
│ [Step: All ▼] [Type: All ▼] [Search: _____________] [🔄 Live]  │
└─────────────────────────────────────────────────────────────────┘
```

### Filter Implementation

```typescript
interface SwimlineFilters {
  step: string | null;
  eventTypes: string[];
  searchQuery: string;
  liveMode: boolean;
}

function filterEvents(events: ADWEvent[], filters: SwimlineFilters) {
  return events.filter(event => {
    if (filters.step && event.step !== filters.step) return false;
    if (filters.eventTypes.length && !filters.eventTypes.includes(event.event_type)) return false;
    if (filters.searchQuery && !event.summary.includes(filters.searchQuery)) return false;
    return true;
  });
}
```

## Event Detail Panel

### Expanded Event View

```text
┌─────────────────────────────────────────────────────────────────┐
│ Event Details                                              [X]  │
├─────────────────────────────────────────────────────────────────┤
│ Type: ToolUseBlock                                              │
│ Step: build                                                     │
│ Time: 2026-01-01 10:32:15 UTC                                  │
│ Duration: 45ms                                                  │
├─────────────────────────────────────────────────────────────────┤
│ Tool: Write                                                     │
│ Path: src/auth/middleware.py                                    │
├─────────────────────────────────────────────────────────────────┤
│ Summary:                                                        │
│ "Implementing JWT validation middleware for API routes"        │
├─────────────────────────────────────────────────────────────────┤
│ Raw Payload:                                                    │
│ {                                                               │
│   "tool_name": "Write",                                        │
│   "file_path": "src/auth/middleware.py",                       │
│   "content": "..."                                             │
│ }                                                               │
└─────────────────────────────────────────────────────────────────┘
```

## Design Checklist

- [ ] Lane layout defined for all steps
- [ ] Event card components designed
- [ ] Status indicators specified
- [ ] Real-time WebSocket integration
- [ ] Event type icons and colors
- [ ] Filter controls designed
- [ ] Event detail panel specified
- [ ] Responsive layout considered
- [ ] Loading and error states
- [ ] Accessibility requirements

## Output Format

```markdown
## Swimlane Visualization Design

### Layout Specification

```text
[ASCII layout diagram]
```

### Lane States

| State | Visual | Trigger |
| --- | --- | --- |
| [state] | [description] | [when applied] |

### Event Cards

[Card specifications per event type]

### Components

**Lane Component:**
[Props, events, slots]

**Event Card Component:**
[Props, events, styling]

**Status Panel:**
[Metrics displayed]

### Real-Time Updates

[WebSocket integration pattern]

### Filtering

[Filter controls and logic]

``` <!-- markdownlint-disable-line MD040 -->

## Anti-Patterns

| Anti-Pattern | Problem | Solution |
| --- | --- | --- |
| No live updates | Stale display | WebSocket streaming |
| Overwhelming events | UI unusable | Filtering and pagination |
| No event details | Can't debug | Detail panel on click |
| Missing status | Unknown state | Status indicators |
| Sync rendering | UI freezes | Buffered updates |

## Cross-References

- @websocket-architecture.md - Real-time streaming
- @hook-event-patterns.md - Event types
- @multi-agent-observability skill - Metrics patterns
- @production-patterns.md - Backend integration

## Version History

- **v1.0.0** (2026-01-01): Initial release (Lesson 14)

---

## Last Updated

**Date:** 2026-01-01
**Model:** claude-opus-4-5-20251101

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
