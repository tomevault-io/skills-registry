---
name: auth-and-authorization
description: > Use when this capability is needed.
metadata:
  author: bramato
---

# Authentication & Authorization — Laravel Sanctum + Inertia + React

## 1. Sanctum SPA Authentication (Inertia Default)

Inertia apps use **cookie-based session authentication** by default. Sanctum provides the
`EnsureFrontendRequestsAreStateful` middleware that enables this for same-domain SPAs.

### Configuration

```php
// config/sanctum.php
return [
    'stateful' => explode(',', env(
        'SANCTUM_STATEFUL_DOMAINS',
        'localhost,localhost:3000,127.0.0.1,127.0.0.1:8000,::1'
    )),
    'guard' => ['web'],
    'expiration' => null, // session-based, no token expiration
];
```

### Middleware Setup (Laravel 11)

```php
// bootstrap/app.php
use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;

return Application::configure(basePath: dirname(__DIR__))
    ->withMiddleware(function (Middleware $middleware) {
        $middleware->statefulApi();
        // This adds EnsureFrontendRequestsAreStateful to the api group

        $middleware->web(append: [
            \App\Http\Middleware\HandleInertiaRequests::class,
        ]);
    })
    ->create();
```

### Authentication Controller

```php
// app/Http/Controllers/Auth/AuthenticatedSessionController.php
namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use App\Http\Requests\Auth\LoginRequest;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Inertia\Inertia;
use Inertia\Response;

class AuthenticatedSessionController extends Controller
{
    public function create(): Response
    {
        return Inertia::render('Auth/Login', [
            'canResetPassword' => route('password.request') !== null,
            'status' => session('status'),
        ]);
    }

    public function store(LoginRequest $request): RedirectResponse
    {
        $request->authenticate();
        $request->session()->regenerate();

        return redirect()->intended(route('dashboard'));
    }

    public function destroy(Request $request): RedirectResponse
    {
        Auth::guard('web')->logout();
        $request->session()->invalidate();
        $request->session()->regenerateToken();

        return redirect('/');
    }
}
```

### Login Request with Rate Limiting

```php
// app/Http/Requests/Auth/LoginRequest.php
namespace App\Http\Requests\Auth;

use Illuminate\Auth\Events\Lockout;
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Support\Str;
use Illuminate\Validation\ValidationException;

class LoginRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'email' => ['required', 'string', 'email'],
            'password' => ['required', 'string'],
        ];
    }

    public function authenticate(): void
    {
        $this->ensureIsNotRateLimited();

        if (! Auth::attempt($this->only('email', 'password'), $this->boolean('remember'))) {
            RateLimiter::hit($this->throttleKey());

            throw ValidationException::withMessages([
                'email' => trans('auth.failed'),
            ]);
        }

        RateLimiter::clear($this->throttleKey());
    }

    private function ensureIsNotRateLimited(): void
    {
        if (! RateLimiter::tooManyAttempts($this->throttleKey(), 5)) {
            return;
        }

        event(new Lockout($this));

        $seconds = RateLimiter::availableIn($this->throttleKey());
        throw ValidationException::withMessages([
            'email' => trans('auth.throttle', [
                'seconds' => $seconds,
                'minutes' => ceil($seconds / 60),
            ]),
        ]);
    }

    private function throttleKey(): string
    {
        return Str::transliterate(Str::lower($this->string('email')).'|'.$this->ip());
    }
}
```

### React Login Page

```tsx
// resources/js/Pages/Auth/Login.tsx
import { useForm, Head } from '@inertiajs/react';
import { FormEventHandler } from 'react';

interface LoginProps {
    canResetPassword: boolean;
    status?: string;
}

export default function Login({ canResetPassword, status }: LoginProps) {
    const { data, setData, post, processing, errors, reset } = useForm({
        email: '',
        password: '',
        remember: false,
    });

    const submit: FormEventHandler = (e) => {
        e.preventDefault();
        post(route('login'), {
            onFinish: () => reset('password'),
        });
    };

    return (
        <>
            <Head title="Log in" />

            {status && <div className="text-sm text-green-600">{status}</div>}

            <form onSubmit={submit}>
                <div>
                    <label htmlFor="email">Email</label>
                    <input
                        id="email"
                        type="email"
                        value={data.email}
                        onChange={(e) => setData('email', e.target.value)}
                        autoFocus
                    />
                    {errors.email && <p className="text-red-600">{errors.email}</p>}
                </div>

                <div>
                    <label htmlFor="password">Password</label>
                    <input
                        id="password"
                        type="password"
                        value={data.password}
                        onChange={(e) => setData('password', e.target.value)}
                    />
                    {errors.password && <p className="text-red-600">{errors.password}</p>}
                </div>

                <div>
                    <label>
                        <input
                            type="checkbox"
                            checked={data.remember}
                            onChange={(e) => setData('remember', e.target.checked)}
                        />
                        <span>Remember me</span>
                    </label>
                </div>

                <button type="submit" disabled={processing}>
                    Log in
                </button>
            </form>
        </>
    );
}
```

---

## 2. Sanctum API Token Authentication

Use token-based auth for mobile apps or third-party API consumers that cannot share cookies.

### Setup

```php
// User model
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;
}
```

### Token Creation with Abilities

```php
// app/Http/Controllers/Api/TokenController.php
namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;

class TokenController extends Controller
{
    public function store(Request $request): JsonResponse
    {
        $request->validate([
            'email' => 'required|email',
            'password' => 'required',
            'device_name' => 'required|string',
            'abilities' => 'sometimes|array',
        ]);

        $user = User::where('email', $request->email)->first();

        if (! $user || ! Hash::check($request->password, $user->password)) {
            throw ValidationException::withMessages([
                'email' => ['The provided credentials are incorrect.'],
            ]);
        }

        $abilities = $request->input('abilities', ['*']);
        $token = $user->createToken($request->device_name, $abilities);

        return response()->json([
            'token' => $token->plainTextToken,
            'abilities' => $abilities,
        ]);
    }

    public function destroy(Request $request): JsonResponse
    {
        // Revoke current token
        $request->user()->currentAccessToken()->delete();

        return response()->json(['message' => 'Token revoked']);
    }
}
```

### Protecting API Routes

```php
// routes/api.php
Route::middleware('auth:sanctum')->group(function () {
    Route::get('/user', fn (Request $request) => $request->user());
    Route::apiResource('projects', ProjectController::class);
});

// Checking abilities in controller
public function update(Request $request, Project $project)
{
    if ($request->user()->tokenCan('project:update')) {
        // proceed
    }
    abort(403);
}
```

---

## 3. Policies

Policies encapsulate authorization logic for a specific model. Laravel auto-discovers policies
when they follow the `App\Policies\{Model}Policy` convention.

### Full CRUD Policy Template

```php
// app/Policies/ProjectPolicy.php
namespace App\Policies;

use App\Enums\Role;
use App\Models\Project;
use App\Models\User;
use Illuminate\Auth\Access\HandlesAuthorization;

class ProjectPolicy
{
    use HandlesAuthorization;

    /**
     * Run before any other check. Return true to grant all, null to continue.
     */
    public function before(User $user, string $ability): ?bool
    {
        if ($user->role === Role::SuperAdmin) {
            return true;
        }

        return null; // fall through to specific check
    }

    public function viewAny(User $user): bool
    {
        return true; // all authenticated users can list
    }

    public function view(User $user, Project $project): bool
    {
        return $user->id === $project->user_id
            || $project->team->users->contains($user);
    }

    public function create(User $user): bool
    {
        return $user->role !== Role::Viewer;
    }

    public function update(User $user, Project $project): bool
    {
        return $user->id === $project->user_id;
    }

    public function delete(User $user, Project $project): bool
    {
        return $user->id === $project->user_id;
    }

    public function restore(User $user, Project $project): bool
    {
        return $user->id === $project->user_id;
    }

    public function forceDelete(User $user, Project $project): bool
    {
        return $user->role === Role::Admin && $user->id === $project->user_id;
    }
}
```

### Using Policies in Controllers

```php
// In a controller
public function update(UpdateProjectRequest $request, Project $project)
{
    $this->authorize('update', $project);
    // or: Gate::authorize('update', $project);
    // or in the request: $request->user()->can('update', $project);

    $project->update($request->validated());
    return back()->with('success', 'Project updated.');
}

// Controller-level authorization via middleware
class ProjectController extends Controller
{
    public function __construct()
    {
        $this->authorizeResource(Project::class, 'project');
    }
}
```

### Generating a Policy

```bash
php artisan make:policy ProjectPolicy --model=Project
```

---

## 4. Gates

Gates are closures that determine if a user can perform a given action. Use gates for actions
that are **not tied to a specific model** (e.g., "access admin panel").

```php
// app/Providers/AppServiceProvider.php (Laravel 11)
use Illuminate\Support\Facades\Gate;

public function boot(): void
{
    Gate::define('access-admin', function (User $user): bool {
        return in_array($user->role, [Role::Admin, Role::SuperAdmin]);
    });

    Gate::define('view-reports', function (User $user): bool {
        return $user->hasPermission('reports.view');
    });
}
```

### Using Gates

```php
// In controllers
if (Gate::allows('access-admin')) {
    // show admin panel
}

// In Blade/Inertia middleware
Gate::authorize('access-admin'); // throws AuthorizationException

// Via middleware on routes
Route::get('/admin', AdminController::class)->middleware('can:access-admin');
```

### When to Use Gates vs Policies

| Use Case | Mechanism |
|----------|-----------|
| Model-level CRUD permissions | **Policy** |
| Non-model actions (access panel, export data) | **Gate** |
| Complex resource ownership rules | **Policy** |
| Simple boolean checks | **Gate** |

---

## 5. Role-Based Access Control (RBAC)

### Enum-Based Roles

```php
// app/Enums/Role.php
namespace App\Enums;

enum Role: string
{
    case SuperAdmin = 'super_admin';
    case Admin = 'admin';
    case Editor = 'editor';
    case Viewer = 'viewer';

    public function label(): string
    {
        return match ($this) {
            self::SuperAdmin => 'Super Admin',
            self::Admin => 'Admin',
            self::Editor => 'Editor',
            self::Viewer => 'Viewer',
        };
    }

    /**
     * Hierarchy: higher index = more permissions.
     */
    public function level(): int
    {
        return match ($this) {
            self::Viewer => 0,
            self::Editor => 1,
            self::Admin => 2,
            self::SuperAdmin => 3,
        };
    }

    public function isAtLeast(self $role): bool
    {
        return $this->level() >= $role->level();
    }
}
```

### Spatie Laravel Permission Integration

```bash
composer require spatie/laravel-permission
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
php artisan migrate
```

```php
// User model
use Spatie\Permission\Traits\HasRoles;

class User extends Authenticatable
{
    use HasApiTokens, HasFactory, HasRoles, Notifiable;
}

// Seeder
$adminRole = Role::create(['name' => 'admin']);
$adminRole->givePermissionTo([
    'projects.create', 'projects.edit', 'projects.delete',
    'users.manage',
]);

$user->assignRole('admin');
$user->hasPermissionTo('projects.create'); // true
```

### Sharing Permissions to Inertia Frontend

```php
// app/Http/Middleware/HandleInertiaRequests.php
public function share(Request $request): array
{
    return [
        ...parent::share($request),
        'auth' => [
            'user' => $request->user() ? [
                'id' => $request->user()->id,
                'name' => $request->user()->name,
                'email' => $request->user()->email,
                'role' => $request->user()->role,
                'permissions' => $request->user()->getAllPermissions()
                    ->pluck('name')
                    ->toArray(),
            ] : null,
        ],
    ];
}
```

### Frontend Permission Checking

```tsx
// resources/js/hooks/usePermission.ts
import { usePage } from '@inertiajs/react';

interface AuthUser {
    id: number;
    name: string;
    email: string;
    role: string;
    permissions: string[];
}

interface PageProps {
    auth: {
        user: AuthUser | null;
    };
}

export function usePermission() {
    const { auth } = usePage<PageProps>().props;

    const can = (permission: string): boolean => {
        return auth.user?.permissions.includes(permission) ?? false;
    };

    const hasRole = (role: string): boolean => {
        return auth.user?.role === role;
    };

    const hasAnyRole = (...roles: string[]): boolean => {
        return roles.includes(auth.user?.role ?? '');
    };

    return { can, hasRole, hasAnyRole, user: auth.user };
}

// Usage in a component
function ProjectActions({ project }: { project: Project }) {
    const { can } = usePermission();

    return (
        <div>
            {can('projects.edit') && (
                <Link href={route('projects.edit', project.id)}>Edit</Link>
            )}
            {can('projects.delete') && (
                <button onClick={() => handleDelete(project.id)}>Delete</button>
            )}
        </div>
    );
}
```

---

## 6. Frontend Auth Patterns

### Accessing Auth Data in Inertia

```tsx
// resources/js/types/index.d.ts
export interface User {
    id: number;
    name: string;
    email: string;
    email_verified_at?: string;
    role: string;
    permissions: string[];
}

export type PageProps<T extends Record<string, unknown> = Record<string, unknown>> = T & {
    auth: {
        user: User;
    };
    flash: {
        success?: string;
        error?: string;
    };
};
```

### Protecting Routes with Middleware

```php
// routes/web.php
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/dashboard', [DashboardController::class, 'index'])->name('dashboard');
    Route::resource('projects', ProjectController::class);
});

// Role-based middleware
Route::middleware(['auth', 'role:admin'])->prefix('admin')->group(function () {
    Route::get('/users', [UserController::class, 'index']);
});
```

### Custom Role Middleware

```php
// app/Http/Middleware/EnsureUserHasRole.php
namespace App\Http\Middleware;

use App\Enums\Role;
use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class EnsureUserHasRole
{
    public function handle(Request $request, Closure $next, string ...$roles): Response
    {
        $userRole = $request->user()?->role;

        if (! $userRole || ! collect($roles)->contains(fn ($r) => $userRole === Role::from($r))) {
            abort(403, 'Insufficient role.');
        }

        return $next($request);
    }
}
```

---

## 7. Password Reset & Email Verification

### Using Laravel Fortify (Headless)

```bash
composer require laravel/fortify
php artisan vendor:publish --provider="Laravel\Fortify\FortifyServiceProvider"
```

```php
// app/Providers/FortifyServiceProvider.php
use Laravel\Fortify\Fortify;
use Inertia\Inertia;

public function boot(): void
{
    Fortify::loginView(fn () => Inertia::render('Auth/Login'));
    Fortify::registerView(fn () => Inertia::render('Auth/Register'));
    Fortify::requestPasswordResetLinkView(fn () => Inertia::render('Auth/ForgotPassword'));
    Fortify::resetPasswordView(fn ($request) => Inertia::render('Auth/ResetPassword', [
        'email' => $request->email,
        'token' => $request->route('token'),
    ]));
    Fortify::verifyEmailView(fn () => Inertia::render('Auth/VerifyEmail'));
}
```

### Email Verification

```php
// User model
class User extends Authenticatable implements MustVerifyEmail
{
    // ...
}

// Routes
Route::middleware(['auth', 'verified'])->group(function () {
    // Only verified users can access these
});
```

---

## 8. Testing Authentication & Authorization

### Testing Login Flow

```php
// tests/Feature/Auth/LoginTest.php
use App\Models\User;

it('renders the login page', function () {
    $this->get('/login')
        ->assertOk()
        ->assertInertia(fn ($page) => $page->component('Auth/Login'));
});

it('authenticates a user with valid credentials', function () {
    $user = User::factory()->create();

    $this->post('/login', [
        'email' => $user->email,
        'password' => 'password',
    ])->assertRedirect('/dashboard');

    $this->assertAuthenticated();
});

it('rejects invalid credentials', function () {
    $user = User::factory()->create();

    $this->post('/login', [
        'email' => $user->email,
        'password' => 'wrong-password',
    ]);

    $this->assertGuest();
});
```

### Testing Policies

```php
// tests/Unit/Policies/ProjectPolicyTest.php
use App\Enums\Role;
use App\Models\Project;
use App\Models\User;
use App\Policies\ProjectPolicy;

it('allows the owner to update a project', function () {
    $user = User::factory()->create();
    $project = Project::factory()->for($user)->create();
    $policy = new ProjectPolicy();

    expect($policy->update($user, $project))->toBeTrue();
});

it('denies non-owners from updating a project', function () {
    $owner = User::factory()->create();
    $other = User::factory()->create();
    $project = Project::factory()->for($owner)->create();
    $policy = new ProjectPolicy();

    expect($policy->update($other, $project))->toBeFalse();
});

it('grants super admins all abilities via before()', function () {
    $admin = User::factory()->create(['role' => Role::SuperAdmin]);
    $project = Project::factory()->create();
    $policy = new ProjectPolicy();

    expect($policy->before($admin, 'delete'))->toBeTrue();
});
```

### Testing Middleware Authorization

```php
it('prevents unauthorized access to admin routes', function () {
    $user = User::factory()->create(['role' => Role::Viewer]);

    $this->actingAs($user)
        ->get('/admin/users')
        ->assertForbidden();
});

it('allows admins to access admin routes', function () {
    $admin = User::factory()->create(['role' => Role::Admin]);

    $this->actingAs($admin)
        ->get('/admin/users')
        ->assertOk();
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bramato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
