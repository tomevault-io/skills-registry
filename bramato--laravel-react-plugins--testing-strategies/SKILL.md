---
name: testing-strategies
description: > Use when this capability is needed.
metadata:
  author: bramato
---

# Testing Strategies — Laravel + Inertia + React

## 1. Testing Pyramid for Laravel + Inertia + React

```
        /  E2E  \           ← Few: Cypress/Playwright (full page flows)
       /----------\
      / Integration \       ← Some: Feature tests (HTTP -> controller -> DB)
     /----------------\
    /    Component      \   ← Many: RTL component tests (React)
   /--------------------\
  /        Unit          \  ← Many: Service, DTO, Model, Hook tests
 /________________________\
```

### What to Test at Each Layer

| Layer | Tool | What to test |
|-------|------|-------------|
| **Unit (PHP)** | Pest | Services, DTOs, value objects, model methods, scopes |
| **Unit (TS)** | Vitest/Jest | Hooks, utilities, formatters, pure functions |
| **Feature (PHP)** | Pest | HTTP request/response cycle, Inertia page rendering, validation, auth |
| **Component (TS)** | RTL + Vitest | React component rendering, user interactions, form submissions |
| **Integration** | Pest | Multi-service flows, queue/event/notification integration |
| **E2E** | Playwright | Critical user journeys (login, checkout, onboarding) |

---

## 2. Laravel Feature Tests

Feature tests exercise the full HTTP stack: routing, middleware, controllers, validation,
database, and Inertia page rendering.

### Testing Inertia Responses

```php
// tests/Feature/Project/IndexTest.php
use App\Models\Project;
use App\Models\User;

it('renders the projects index page with data', function () {
    $user = User::factory()->create();
    $projects = Project::factory()->count(3)->for($user)->create();

    $this->actingAs($user)
        ->get(route('projects.index'))
        ->assertOk()
        ->assertInertia(fn ($page) => $page
            ->component('Projects/Index')
            ->has('projects.data', 3)
            ->has('projects.data.0', fn ($project) => $project
                ->has('id')
                ->has('title')
                ->has('status')
                ->etc()
            )
        );
});
```

### Testing Store / Update / Delete

```php
it('creates a project and redirects', function () {
    $user = User::factory()->create();

    $this->actingAs($user)
        ->post(route('projects.store'), [
            'title' => 'New Project',
            'description' => 'A test project',
        ])
        ->assertRedirect(route('projects.index'))
        ->assertSessionHas('success', 'Project created.');

    $this->assertDatabaseHas('projects', [
        'title' => 'New Project',
        'user_id' => $user->id,
    ]);
});

it('validates required fields on store', function () {
    $user = User::factory()->create();

    $this->actingAs($user)
        ->post(route('projects.store'), [])
        ->assertSessionHasErrors(['title', 'description']);
});

it('updates a project', function () {
    $user = User::factory()->create();
    $project = Project::factory()->for($user)->create();

    $this->actingAs($user)
        ->put(route('projects.update', $project), [
            'title' => 'Updated Title',
            'description' => $project->description,
        ])
        ->assertRedirect();

    expect($project->fresh()->title)->toBe('Updated Title');
});

it('soft deletes a project', function () {
    $user = User::factory()->create();
    $project = Project::factory()->for($user)->create();

    $this->actingAs($user)
        ->delete(route('projects.destroy', $project))
        ->assertRedirect(route('projects.index'));

    $this->assertSoftDeleted($project);
});
```

### Testing Flash Messages

```php
it('flashes a success message on create', function () {
    $user = User::factory()->create();

    $response = $this->actingAs($user)
        ->post(route('projects.store'), [
            'title' => 'Test',
            'description' => 'Test desc',
        ]);

    $response->assertSessionHas('success');
});
```

### Testing Authorization

```php
it('denies access to unauthorized users', function () {
    $owner = User::factory()->create();
    $other = User::factory()->create();
    $project = Project::factory()->for($owner)->create();

    $this->actingAs($other)
        ->put(route('projects.update', $project), ['title' => 'Hacked'])
        ->assertForbidden();
});
```

---

## 3. Laravel Unit Tests

Unit tests do not boot the full framework. They test single classes in isolation.

### Testing a Service

```php
// tests/Unit/Services/ProjectServiceTest.php
use App\DTOs\CreateProjectDTO;
use App\Models\Project;
use App\Models\User;
use App\Services\ProjectService;

beforeEach(function () {
    $this->service = app(ProjectService::class);
    $this->user = User::factory()->create();
});

it('creates a project from DTO', function () {
    $dto = new CreateProjectDTO(
        title: 'Test Project',
        description: 'Description',
        userId: $this->user->id,
    );

    $project = $this->service->create($dto);

    expect($project)
        ->toBeInstanceOf(Project::class)
        ->title->toBe('Test Project')
        ->user_id->toBe($this->user->id);
});

it('throws when creating with invalid data', function () {
    $dto = new CreateProjectDTO(
        title: '',
        description: '',
        userId: $this->user->id,
    );

    $this->service->create($dto);
})->throws(\InvalidArgumentException::class);
```

### Testing a DTO

```php
// tests/Unit/DTOs/CreateProjectDTOTest.php
use App\DTOs\CreateProjectDTO;

it('creates a DTO from request data', function () {
    $dto = CreateProjectDTO::fromArray([
        'title' => 'My Project',
        'description' => 'Some description',
        'user_id' => 1,
    ]);

    expect($dto)
        ->title->toBe('My Project')
        ->description->toBe('Some description')
        ->userId->toBe(1);
});
```

### Testing Model Scopes and Accessors

```php
// tests/Unit/Models/ProjectTest.php
use App\Models\Project;

it('scopes to active projects', function () {
    Project::factory()->active()->count(2)->create();
    Project::factory()->archived()->count(3)->create();

    expect(Project::active()->count())->toBe(2);
});

it('returns the formatted deadline', function () {
    $project = Project::factory()->make(['deadline' => '2025-06-15']);

    expect($project->formatted_deadline)->toBe('Jun 15, 2025');
});
```

---

## 4. Pest Syntax Reference

### Basic Structure

```php
// test() style
test('it can create a project', function () {
    // arrange, act, assert
});

// it() style (BDD)
it('creates a project', function () {
    // ...
});

// describe() blocks for grouping
describe('ProjectService', function () {
    describe('create', function () {
        it('creates a project from valid data', function () { /* ... */ });
        it('throws on invalid data', function () { /* ... */ });
    });

    describe('delete', function () {
        it('soft-deletes the project', function () { /* ... */ });
    });
});
```

### Expect Chains

```php
expect($value)->toBe('exact');
expect($value)->toEqual(['key' => 'value']); // loose comparison
expect($value)->toBeTrue();
expect($value)->toBeFalse();
expect($value)->toBeNull();
expect($value)->toBeEmpty();
expect($value)->toBeInstanceOf(Project::class);
expect($value)->toContain('substring');
expect($collection)->toHaveCount(3);
expect($value)->toBeGreaterThan(10);
expect($value)->toBeBetween(1, 100);
expect($value)->toMatchArray(['key' => 'value']);
expect($value)->toHaveKey('nested.key');
expect($value)->each->toBeString();

// Higher-order expectations
expect($user)
    ->name->toBe('John')
    ->email->toContain('@')
    ->role->not->toBeNull();
```

### Datasets (Data Providers)

```php
it('validates disallowed statuses', function (string $status) {
    $this->actingAs(User::factory()->create())
        ->post(route('projects.store'), [
            'title' => 'Test',
            'status' => $status,
        ])
        ->assertSessionHasErrors('status');
})->with(['invalid', 'unknown', '']);

// Named datasets
dataset('invalid_emails', [
    'missing @' => ['notanemail'],
    'missing domain' => ['user@'],
    'empty string' => [''],
]);

it('rejects invalid emails', function (string $email) {
    // ...
})->with('invalid_emails');
```

### Lifecycle Hooks

```php
beforeEach(function () {
    $this->user = User::factory()->create();
    $this->service = app(ProjectService::class);
});

afterEach(function () {
    Cache::flush();
});

beforeAll(function () {
    // Runs once before all tests in the file
});
```

---

## 5. Database Testing

### RefreshDatabase Trait

```php
// Pest: globally in Pest.php
uses(Illuminate\Foundation\Testing\RefreshDatabase::class)->in('Feature');
uses(Illuminate\Foundation\Testing\LazilyRefreshDatabase::class)->in('Feature');
// LazilyRefreshDatabase wraps each test in a transaction (faster than migrating each time)
```

### Factory Patterns

```php
// database/factories/ProjectFactory.php
namespace Database\Factories;

use App\Enums\ProjectStatus;
use App\Models\Project;
use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class ProjectFactory extends Factory
{
    protected $model = Project::class;

    public function definition(): array
    {
        return [
            'user_id' => User::factory(),
            'title' => fake()->sentence(3),
            'description' => fake()->paragraph(),
            'status' => ProjectStatus::Active,
            'deadline' => fake()->dateTimeBetween('now', '+1 year'),
        ];
    }

    public function active(): static
    {
        return $this->state(fn () => ['status' => ProjectStatus::Active]);
    }

    public function archived(): static
    {
        return $this->state(fn () => ['status' => ProjectStatus::Archived]);
    }

    public function withTasks(int $count = 3): static
    {
        return $this->has(Task::factory()->count($count));
    }
}

// Usage in tests
$project = Project::factory()
    ->for($user)
    ->active()
    ->withTasks(5)
    ->create();
```

### Database Assertions

```php
$this->assertDatabaseHas('projects', [
    'title' => 'New Project',
    'user_id' => $user->id,
]);

$this->assertDatabaseMissing('projects', [
    'title' => 'Deleted Project',
]);

$this->assertDatabaseCount('projects', 5);

$this->assertSoftDeleted('projects', [
    'id' => $project->id,
]);

$this->assertModelExists($project);
$this->assertModelMissing($deletedProject);
```

---

## 6. Mocking

### Faking Laravel Services

```php
use Illuminate\Support\Facades\Mail;
use Illuminate\Support\Facades\Event;
use Illuminate\Support\Facades\Notification;
use Illuminate\Support\Facades\Queue;
use Illuminate\Support\Facades\Storage;

it('sends a welcome email on registration', function () {
    Mail::fake();

    $this->post(route('register'), [
        'name' => 'John',
        'email' => 'john@example.com',
        'password' => 'password',
        'password_confirmation' => 'password',
    ]);

    Mail::assertSent(WelcomeMail::class, function ($mail) {
        return $mail->hasTo('john@example.com');
    });
});

it('dispatches ProjectCreated event', function () {
    Event::fake([ProjectCreated::class]);

    $user = User::factory()->create();
    $this->actingAs($user)->post(route('projects.store'), [
        'title' => 'Test',
        'description' => 'Test desc',
    ]);

    Event::assertDispatched(ProjectCreated::class, function ($event) {
        return $event->project->title === 'Test';
    });
});

it('queues an export job', function () {
    Queue::fake();

    $user = User::factory()->create();
    $this->actingAs($user)->post(route('projects.export'));

    Queue::assertPushed(ExportProjectsJob::class);
});

it('stores an uploaded file', function () {
    Storage::fake('s3');

    $file = UploadedFile::fake()->image('avatar.jpg');
    $this->actingAs(User::factory()->create())
        ->post(route('profile.avatar'), ['avatar' => $file]);

    Storage::disk('s3')->assertExists('avatars/' . $file->hashName());
});
```

### Mocking Services in the Container

```php
use App\Services\PaymentGateway;
use Mockery;

it('processes payment through gateway', function () {
    $mock = Mockery::mock(PaymentGateway::class);
    $mock->shouldReceive('charge')
        ->once()
        ->with(1000, 'usd')
        ->andReturn(true);

    $this->app->instance(PaymentGateway::class, $mock);

    $this->actingAs(User::factory()->create())
        ->post(route('payments.store'), ['amount' => 1000]);
});

// Using Pest's mock() helper
it('calls external API', function () {
    $this->mock(ExternalApiService::class)
        ->shouldReceive('fetch')
        ->once()
        ->andReturn(['data' => 'value']);

    // test code that uses ExternalApiService
});
```

### Partial Mocks and Spies

```php
// Spy: verify after the fact (no expectations upfront)
it('logs the activity', function () {
    $spy = $this->spy(ActivityLogger::class);

    $this->actingAs(User::factory()->create())
        ->post(route('projects.store'), ['title' => 'Test', 'description' => 'Desc']);

    $spy->shouldHaveReceived('log')
        ->with('project.created', Mockery::type(Project::class));
});

// Partial mock: only mock specific methods
it('uses real methods except external call', function () {
    $this->partialMock(ProjectService::class, function ($mock) {
        $mock->shouldReceive('notifySlack')->andReturn(true);
    });

    // Other methods on ProjectService remain real
});
```

---

## 7. React Component Tests

### Setup with Vitest and RTL

```ts
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';

export default defineConfig({
    plugins: [react()],
    test: {
        environment: 'jsdom',
        setupFiles: ['./tests/js/setup.ts'],
        globals: true,
    },
    resolve: {
        alias: {
            '@': '/resources/js',
        },
    },
});
```

```ts
// tests/js/setup.ts
import '@testing-library/jest-dom/vitest';
```

### Testing a Component

```tsx
// tests/js/Components/ProjectCard.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ProjectCard } from '@/Components/ProjectCard';

const project = {
    id: 1,
    title: 'Test Project',
    status: 'active',
    tasksCount: 5,
};

describe('ProjectCard', () => {
    it('renders project information', () => {
        render(<ProjectCard project={project} />);

        expect(screen.getByText('Test Project')).toBeInTheDocument();
        expect(screen.getByText('active')).toBeInTheDocument();
        expect(screen.getByText('5 tasks')).toBeInTheDocument();
    });

    it('calls onDelete when delete button is clicked', async () => {
        const user = userEvent.setup();
        const onDelete = vi.fn();

        render(<ProjectCard project={project} onDelete={onDelete} />);

        await user.click(screen.getByRole('button', { name: /delete/i }));

        expect(onDelete).toHaveBeenCalledWith(1);
    });

    it('shows edit link for authorized users', () => {
        render(<ProjectCard project={project} canEdit={true} />);

        expect(screen.getByRole('link', { name: /edit/i })).toBeInTheDocument();
    });

    it('hides edit link for unauthorized users', () => {
        render(<ProjectCard project={project} canEdit={false} />);

        expect(screen.queryByRole('link', { name: /edit/i })).not.toBeInTheDocument();
    });
});
```

### Testing Form Components

```tsx
// tests/js/Pages/Projects/Create.test.tsx
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Create from '@/Pages/Projects/Create';

// Mock Inertia's useForm
const mockPost = vi.fn();
vi.mock('@inertiajs/react', async () => {
    const actual = await vi.importActual('@inertiajs/react');
    return {
        ...actual,
        useForm: () => ({
            data: { title: '', description: '' },
            setData: vi.fn(),
            post: mockPost,
            processing: false,
            errors: {},
            reset: vi.fn(),
        }),
        Head: ({ title }: { title: string }) => <title>{title}</title>,
    };
});

// Mock route helper
vi.stubGlobal('route', (name: string) => `/mocked/${name}`);

describe('Create Project Page', () => {
    it('renders the create form', () => {
        render(<Create />);

        expect(screen.getByLabelText(/title/i)).toBeInTheDocument();
        expect(screen.getByLabelText(/description/i)).toBeInTheDocument();
        expect(screen.getByRole('button', { name: /create/i })).toBeInTheDocument();
    });

    it('submits the form', async () => {
        const user = userEvent.setup();
        render(<Create />);

        await user.type(screen.getByLabelText(/title/i), 'New Project');
        await user.type(screen.getByLabelText(/description/i), 'A description');
        await user.click(screen.getByRole('button', { name: /create/i }));

        expect(mockPost).toHaveBeenCalled();
    });
});
```

---

## 8. React Hook Tests

```tsx
// tests/js/hooks/usePermission.test.ts
import { renderHook } from '@testing-library/react';
import { usePermission } from '@/hooks/usePermission';

vi.mock('@inertiajs/react', () => ({
    usePage: () => ({
        props: {
            auth: {
                user: {
                    id: 1,
                    name: 'Test User',
                    role: 'admin',
                    permissions: ['projects.create', 'projects.edit'],
                },
            },
        },
    }),
}));

describe('usePermission', () => {
    it('returns true for granted permissions', () => {
        const { result } = renderHook(() => usePermission());

        expect(result.current.can('projects.create')).toBe(true);
    });

    it('returns false for denied permissions', () => {
        const { result } = renderHook(() => usePermission());

        expect(result.current.can('users.delete')).toBe(false);
    });

    it('checks role correctly', () => {
        const { result } = renderHook(() => usePermission());

        expect(result.current.hasRole('admin')).toBe(true);
        expect(result.current.hasRole('viewer')).toBe(false);
    });
});
```

### Testing Async Hooks

```tsx
// tests/js/hooks/useProjects.test.ts
import { renderHook, waitFor } from '@testing-library/react';
import { useProjects } from '@/hooks/useProjects';

describe('useProjects', () => {
    it('starts in loading state', () => {
        const { result } = renderHook(() => useProjects());

        expect(result.current.isLoading).toBe(true);
        expect(result.current.projects).toEqual([]);
    });

    it('returns projects after loading', async () => {
        const { result } = renderHook(() => useProjects());

        await waitFor(() => {
            expect(result.current.isLoading).toBe(false);
        });

        expect(result.current.projects).toHaveLength(3);
    });
});
```

---

## 9. API Integration Tests with MSW

Mock Service Worker intercepts network requests at the service worker level for realistic
API testing in React components.

```ts
// tests/js/mocks/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
    http.get('/api/projects', () => {
        return HttpResponse.json({
            data: [
                { id: 1, title: 'Project A', status: 'active' },
                { id: 2, title: 'Project B', status: 'archived' },
            ],
        });
    }),

    http.post('/api/projects', async ({ request }) => {
        const body = await request.json();
        return HttpResponse.json(
            { data: { id: 3, ...body } },
            { status: 201 }
        );
    }),

    http.delete('/api/projects/:id', ({ params }) => {
        return new HttpResponse(null, { status: 204 });
    }),
];
```

```ts
// tests/js/mocks/server.ts
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);

// tests/js/setup.ts
import { server } from './mocks/server';

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

```tsx
// tests/js/Components/ProjectList.test.tsx
import { render, screen, waitFor } from '@testing-library/react';
import { server } from '../mocks/server';
import { http, HttpResponse } from 'msw';
import { ProjectList } from '@/Components/ProjectList';

it('shows projects loaded from API', async () => {
    render(<ProjectList />);

    await waitFor(() => {
        expect(screen.getByText('Project A')).toBeInTheDocument();
        expect(screen.getByText('Project B')).toBeInTheDocument();
    });
});

it('shows error message on API failure', async () => {
    server.use(
        http.get('/api/projects', () => {
            return HttpResponse.json(
                { message: 'Server Error' },
                { status: 500 }
            );
        })
    );

    render(<ProjectList />);

    await waitFor(() => {
        expect(screen.getByText(/error loading projects/i)).toBeInTheDocument();
    });
});
```

---

## 10. Test Organization

### Directory Structure

```
tests/
├── Feature/
│   ├── Auth/
│   │   ├── LoginTest.php
│   │   └── RegistrationTest.php
│   ├── Project/
│   │   ├── CreateProjectTest.php
│   │   ├── DeleteProjectTest.php
│   │   ├── IndexProjectTest.php
│   │   └── UpdateProjectTest.php
│   └── Api/
│       └── ProjectApiTest.php
├── Unit/
│   ├── DTOs/
│   │   └── CreateProjectDTOTest.php
│   ├── Models/
│   │   └── ProjectTest.php
│   ├── Policies/
│   │   └── ProjectPolicyTest.php
│   └── Services/
│       └── ProjectServiceTest.php
├── Pest.php              ← Global config, uses() calls
└── TestCase.php

tests/js/
├── Components/
│   ├── ProjectCard.test.tsx
│   └── ProjectList.test.tsx
├── Pages/
│   └── Projects/
│       ├── Create.test.tsx
│       └── Index.test.tsx
├── hooks/
│   └── usePermission.test.ts
├── mocks/
│   ├── handlers.ts
│   └── server.ts
└── setup.ts
```

### Pest.php Configuration

```php
// tests/Pest.php
uses(Tests\TestCase::class)->in('Feature', 'Unit');
uses(Illuminate\Foundation\Testing\RefreshDatabase::class)->in('Feature');

// Global helper
function actingAsAdmin(): Tests\TestCase
{
    $admin = \App\Models\User::factory()->create([
        'role' => \App\Enums\Role::Admin,
    ]);

    return test()->actingAs($admin);
}
```

### Naming Conventions

- PHP test files: `{Action}{Model}Test.php` (e.g., `CreateProjectTest.php`)
- React test files: `{ComponentName}.test.tsx`
- Test descriptions: start with `it` + present-tense verb (e.g., `it('creates a project')`)
- Group related tests with `describe()` blocks
- One assertion concept per test (a test can have multiple `expect()` calls if they assert the same concept)

### Running Tests

```bash
# PHP tests
php artisan test                      # all tests
php artisan test --filter=ProjectTest # filter by name
php artisan test --parallel           # parallel execution
php artisan test --coverage           # with coverage (requires Xdebug/PCOV)
./vendor/bin/pest --dirty             # only test files changed since last commit

# JavaScript tests
npx vitest                           # watch mode
npx vitest run                       # single run
npx vitest run --coverage            # with coverage
npx vitest run tests/js/Components   # specific directory
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bramato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
