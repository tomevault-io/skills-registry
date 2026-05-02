---
name: action-pattern
description: >- Use when this capability is needed.
metadata:
  author: percymamedy
---

# Action Pattern Development

## When to Apply

Activate this skill when:

- Implementing new business logic that doesn't belong in a controller
- Creating a feature that involves multiple steps
- Refactoring complex controller methods
- Extracting reusable logic from models
- User mentions "action", "use case", or "business logic"

## Project Convention

This project uses single-purpose Action classes for business logic. See `.ai/guidelines/architecture.md` for full details.

## Creating an Action

### File Location

All actions go in `app/Actions/` directory.

### Naming Convention

`{Verb}{Noun}Action.php`

Examples:
- `CreateTaskAction.php`
- `CompleteTaskAction.php`
- `SendWelcomeEmailAction.php`
- `CalculateOrderTotalAction.php`

### Basic Structure

<code-snippet name="Action Class Template" lang="php">
<?php

declare(strict_types=1);

namespace App\Actions;

class {Verb}{Noun}Action
{
    public function __construct(
        // Inject dependencies here
    ) {
    }

    public function execute(/* parameters */): mixed
    {
        // Business logic here
    }
}
</code-snippet>

### Example: Complete Task Action

<code-snippet name="CompleteTaskAction Example" lang="php">
<?php

declare(strict_types=1);

namespace App\Actions;

use App\Events\TaskCompleted;
use App\Models\Task;

class CompleteTaskAction
{
    public function execute(Task $task): Task
    {
        $task->update([
            'is_completed' => true,
            'completed_at' => now(),
        ]);

        TaskCompleted::dispatch($task);

        return $task;
    }
}
</code-snippet>

### Example: Action with Dependencies

<code-snippet name="Action with Dependencies" lang="php">
<?php

declare(strict_types=1);

namespace App\Actions;

use App\Models\Task;
use App\Models\User;
use App\Notifications\TaskAssigned;

class AssignTaskAction
{
    public function execute(Task $task, User $assignee): Task
    {
        $task->update(['assignee_id' => $assignee->id]);

        $assignee->notify(new TaskAssigned($task));

        return $task;
    }
}
</code-snippet>

## Using Actions

### In Controllers

<code-snippet name="Using Action in Controller" lang="php">
class TaskController extends Controller
{
    public function complete(Task $task, CompleteTaskAction $action)
    {
        $action->execute($task);

        return redirect()->route('tasks.index')
            ->with('success', 'Task completed!');
    }
}
</code-snippet>

### In Livewire Components

<code-snippet name="Using Action in Livewire" lang="php">
public function completeTask(Task $task, CompleteTaskAction $action): void
{
    $action->execute($task);

    $this->dispatch('task-completed');
}
</code-snippet>

### In Commands

<code-snippet name="Using Action in Command" lang="php">
public function handle(CompleteTaskAction $action): int
{
    $tasks = Task::overdue()->get();

    foreach ($tasks as $task) {
        $action->execute($task);
    }

    return Command::SUCCESS;
}
</code-snippet>

## Testing Actions

<code-snippet name="Testing an Action" lang="php">
<?php

use App\Actions\CompleteTaskAction;
use App\Events\TaskCompleted;
use App\Models\Task;

test('complete task action marks task as completed', function () {
    Event::fake();

    $task = Task::factory()->create(['is_completed' => false]);
    $action = new CompleteTaskAction();

    $result = $action->execute($task);

    expect($result->is_completed)->toBeTrue()
        ->and($result->completed_at)->not->toBeNull();

    Event::assertDispatched(TaskCompleted::class);
});
</code-snippet>

## Best Practices

1. **Single Responsibility**: Each action does ONE thing
2. **Return Values**: Always return the affected model or a meaningful result
3. **Type Hints**: Use strict types and return type declarations
4. **Events**: Dispatch domain events for side effects
5. **No HTTP Concerns**: Actions should not know about requests/responses
6. **Testability**: Actions should be easy to unit test in isolation

## When NOT to Use Actions

- Simple CRUD with no business logic (use controller directly)
- Query-only operations (use Query classes instead)
- External API integrations (use Service classes instead)

## Verification Checklist

- [ ] Action is in `app/Actions/` directory
- [ ] Class name follows `{Verb}{Noun}Action` pattern
- [ ] Has single `execute()` method
- [ ] Uses strict types declaration
- [ ] Dependencies injected via constructor
- [ ] Returns meaningful result
- [ ] Has corresponding test

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/percymamedy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
