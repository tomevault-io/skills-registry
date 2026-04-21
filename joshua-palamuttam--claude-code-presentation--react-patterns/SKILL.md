---
name: react-patterns
description: React component patterns including functional components, hooks, state management, API integration, loading/error states, and event handlers. Use when creating or reviewing React components. Use when this capability is needed.
metadata:
  author: joshua-palamuttam
---

# React Patterns

## Overview
These patterns ensure consistent, maintainable React components with TypeScript. Follow these guidelines for all frontend development.

## Component Structure

### Functional Components Only
Always use functional components with hooks:

```tsx
export function TaskList({ tasks, onTaskClick }: TaskListProps) {
  return (
    <ul className="task-list">
      {tasks.map(task => (
        <TaskItem key={task.id} task={task} onClick={onTaskClick} />
      ))}
    </ul>
  );
}
```

### Component File Structure
```
components/
├── TaskList/
│   ├── TaskList.tsx       # Component implementation
│   ├── TaskList.css       # Component styles
│   └── index.ts           # Re-export
```

## TypeScript Interfaces

### Props Interfaces
Define props interfaces for every component:

```tsx
interface TaskListProps {
  tasks: Task[];
  onTaskClick: (task: Task) => void;
  isLoading?: boolean;
}

export function TaskList({ tasks, onTaskClick, isLoading = false }: TaskListProps) {
  // ...
}
```

## State Management

### useState for Local State
```tsx
const [title, setTitle] = useState('');
const [isSubmitting, setIsSubmitting] = useState(false);
```

### useEffect for Side Effects
```tsx
useEffect(() => {
  fetchTasks();
}, []);
```

## Loading and Error States

### Always Handle All States
```tsx
if (isLoading) {
  return <div className="loading">Loading tasks...</div>;
}

if (error) {
  return <div className="error">Error: {error}</div>;
}

if (tasks.length === 0) {
  return <div className="empty">No tasks yet. Create one!</div>;
}
```

## Event Handlers

### Naming Convention
Prefix with `handle` for component handlers, `on` for props:

```tsx
interface TaskItemProps {
  task: Task;
  onComplete: (id: string) => void;  // Prop callback
}

function TaskItem({ task, onComplete }: TaskItemProps) {
  function handleCompleteClick() {  // Local handler
    onComplete(task.id);
  }
  // ...
}
```

## CSS Styling

### Component-Scoped CSS
Each component has its own CSS file with BEM-like naming:
- `.task-list` - Block
- `.task-item` - Element
- `.task-item.completed` - Modifier

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joshua-palamuttam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
