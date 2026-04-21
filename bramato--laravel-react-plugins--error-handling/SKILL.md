---
name: error-handling
description: Use when implementing error handling, exception management, error boundaries, or structured error responses across Laravel and React. Covers custom exceptions, API error formats, React Error Boundaries, form validation errors, and logging strategies.
metadata:
  author: bramato
---

# Error Handling — Laravel + Inertia + React

Comprehensive patterns for handling errors across the full stack: Laravel exception handling, custom domain exceptions, API error formats, React Error Boundaries, Inertia error handling, and logging strategies.

## 1. Laravel Exception Handler Customization

Laravel 11+ uses `bootstrap/app.php` for exception handling configuration. Earlier versions use `app/Exceptions/Handler.php`.

### Laravel 11+ Configuration

```php
// bootstrap/app.php
use Illuminate\Foundation\Application;
use Illuminate\Foundation\Configuration\Exceptions;
use Illuminate\Http\Request;
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;

return Application::configure(basePath: dirname(__DIR__))
    ->withExceptions(function (Exceptions $exceptions) {

        // Render custom responses for specific exceptions
        $exceptions->render(function (NotFoundHttpException $e, Request $request) {
            if ($request->is('api/*')) {
                return response()->json([
                    'error'   => 'resource_not_found',
                    'message' => 'The requested resource was not found.',
                ], 404);
            }
        });

        // Handle domain exceptions
        $exceptions->render(function (OrderNotCancellableException $e, Request $request) {
            if ($request->is('api/*')) {
                return response()->json([
                    'error'   => 'order_not_cancellable',
                    'message' => $e->getMessage(),
                ], 422);
            }

            return back()->with('error', $e->getMessage());
        });

        // Report to external services
        $exceptions->report(function (OrderNotCancellableException $e) {
            // ~~monitoring: Log to Sentry/Bugsnag
            // Sentry::captureException($e);
            logger()->warning('Order cancellation attempted', [
                'order_id' => $e->orderId,
            ]);
        });

        // Don't report certain exceptions
        $exceptions->dontReport([
            OrderNotCancellableException::class,
            InsufficientInventoryException::class,
        ]);

    })->create();
```

### Laravel 10 Handler Pattern

```php
// app/Exceptions/Handler.php
namespace App\Exceptions;

use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler;
use Throwable;

class Handler extends ExceptionHandler
{
    protected $dontReport = [
        OrderNotCancellableException::class,
    ];

    public function register(): void
    {
        $this->renderable(function (NotFoundHttpException $e, Request $request) {
            if ($request->is('api/*')) {
                return response()->json([
                    'error'   => 'resource_not_found',
                    'message' => 'The requested resource was not found.',
                ], 404);
            }
        });

        $this->reportable(function (Throwable $e) {
            // ~~monitoring: Sentry integration
            // if (app()->bound('sentry')) {
            //     app('sentry')->captureException($e);
            // }
        });
    }
}
```

---

## 2. Custom Domain Exceptions

Create specific exception classes for domain-level error conditions. These make error handling explicit and testable.

### Base Domain Exception

```php
<?php

namespace App\Exceptions;

use RuntimeException;

abstract class DomainException extends RuntimeException
{
    public function __construct(
        string $message,
        public readonly ?string $errorCode = null,
        public readonly array $context = [],
        int $code = 0,
        ?\Throwable $previous = null,
    ) {
        parent::__construct($message, $code, $previous);
    }

    /**
     * HTTP status code for this exception when rendered as a response.
     */
    public function statusCode(): int
    {
        return 422;
    }

    /**
     * Structured error payload for API responses.
     */
    public function toArray(): array
    {
        return [
            'error'   => $this->errorCode ?? 'domain_error',
            'message' => $this->getMessage(),
        ];
    }
}
```

### Specific Domain Exceptions

```php
<?php

namespace App\Exceptions;

use App\Models\Order;

class OrderNotCancellableException extends DomainException
{
    public readonly int $orderId;

    public static function forOrder(Order $order): self
    {
        $instance = new self(
            message: "Order #{$order->id} with status '{$order->status->label()}' cannot be cancelled.",
            errorCode: 'order_not_cancellable',
            context: ['order_id' => $order->id, 'status' => $order->status->value],
        );
        $instance->orderId = $order->id;

        return $instance;
    }

    public function statusCode(): int
    {
        return 422;
    }
}
```

```php
<?php

namespace App\Exceptions;

class InsufficientInventoryException extends DomainException
{
    public static function forProduct(int $productId, int $requested, int $available): self
    {
        return new self(
            message: "Insufficient inventory for product #{$productId}. Requested: {$requested}, Available: {$available}.",
            errorCode: 'insufficient_inventory',
            context: compact('productId', 'requested', 'available'),
        );
    }

    public function statusCode(): int
    {
        return 409;
    }
}
```

```php
<?php

namespace App\Exceptions;

class PaymentFailedException extends DomainException
{
    public static function declined(string $reason): self
    {
        return new self(
            message: "Payment was declined: {$reason}",
            errorCode: 'payment_declined',
            context: ['reason' => $reason],
        );
    }

    public static function gatewayTimeout(): self
    {
        return new self(
            message: 'Payment gateway did not respond in time. Please try again.',
            errorCode: 'payment_gateway_timeout',
        );
    }

    public function statusCode(): int
    {
        return 402;
    }
}
```

### Using Domain Exceptions in Services

```php
class OrderService
{
    public function cancel(Order $order): Order
    {
        if (! $order->status->isCancellable()) {
            throw OrderNotCancellableException::forOrder($order);
        }

        return DB::transaction(function () use ($order) {
            $order->update(['status' => OrderStatus::Cancelled]);
            event(new OrderCancelled($order));
            return $order->fresh();
        });
    }
}
```

---

## 3. API Error Response Format

Maintain a consistent JSON error structure across the entire API.

### Standard Error Response Structure

```json
{
    "error": "error_code_snake_case",
    "message": "Human-readable description of what went wrong.",
    "errors": {
        "field_name": ["Validation message for this field."]
    },
    "meta": {
        "request_id": "uuid-here",
        "timestamp": "2025-01-15T10:30:00Z"
    }
}
```

### Error Response Helper Trait

```php
<?php

namespace App\Http\Traits;

use Illuminate\Http\JsonResponse;

trait ApiResponses
{
    protected function errorResponse(
        string $error,
        string $message,
        int $status = 400,
        array $errors = [],
    ): JsonResponse {
        $payload = [
            'error'   => $error,
            'message' => $message,
        ];

        if (! empty($errors)) {
            $payload['errors'] = $errors;
        }

        return response()->json($payload, $status);
    }

    protected function validationErrorResponse(array $errors): JsonResponse
    {
        return $this->errorResponse(
            error: 'validation_error',
            message: 'The given data was invalid.',
            status: 422,
            errors: $errors,
        );
    }

    protected function notFoundResponse(string $message = 'Resource not found.'): JsonResponse
    {
        return $this->errorResponse('not_found', $message, 404);
    }

    protected function unauthorizedResponse(string $message = 'Unauthorized.'): JsonResponse
    {
        return $this->errorResponse('unauthorized', $message, 401);
    }

    protected function forbiddenResponse(string $message = 'Forbidden.'): JsonResponse
    {
        return $this->errorResponse('forbidden', $message, 403);
    }
}
```

### HTTP Status Code Mapping

| Status Code | Error Code | When to Use |
|------------|------------|-------------|
| 400 | `bad_request` | Malformed request, missing required headers |
| 401 | `unauthorized` | Missing or invalid authentication |
| 402 | `payment_required` | Payment failed or required |
| 403 | `forbidden` | Authenticated but not authorized |
| 404 | `not_found` | Resource does not exist |
| 409 | `conflict` | State conflict (e.g., duplicate, insufficient inventory) |
| 422 | `validation_error` | Validation failed, domain rule violation |
| 423 | `locked` | Resource is locked for editing |
| 429 | `rate_limited` | Too many requests |
| 500 | `server_error` | Unexpected server error |
| 503 | `service_unavailable` | Maintenance or dependency outage |

---

## 4. Validation Error Handling

### FormRequest Validation with Inertia

Inertia automatically handles validation errors. When a FormRequest fails, Laravel redirects back with errors in the session. Inertia picks these up and makes them available via `usePage().props.errors`.

```php
// app/Http/Requests/Order/StoreOrderRequest.php
class StoreOrderRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'customer_name'  => ['required', 'string', 'max:255'],
            'customer_email' => ['required', 'email', 'max:255'],
            'items'          => ['required', 'array', 'min:1'],
            'items.*.product_id' => ['required', 'exists:products,id'],
            'items.*.quantity'   => ['required', 'integer', 'min:1'],
        ];
    }

    public function messages(): array
    {
        return [
            'items.required' => 'At least one item is required.',
            'items.min'      => 'At least one item is required.',
            'items.*.product_id.exists' => 'The selected product does not exist.',
        ];
    }
}
```

### Form Error Display in React (useForm)

```tsx
import { useForm } from '@inertiajs/react';

export default function CreateOrder() {
    const { data, setData, post, processing, errors, reset } = useForm({
        customer_name: '',
        customer_email: '',
        items: [{ product_id: '', quantity: 1 }],
    });

    const handleSubmit = (e: React.FormEvent) => {
        e.preventDefault();
        post(route('orders.store'), {
            onSuccess: () => reset(),
        });
    };

    return (
        <form onSubmit={handleSubmit}>
            <div>
                <label htmlFor="customer_name">Customer Name</label>
                <input
                    id="customer_name"
                    value={data.customer_name}
                    onChange={e => setData('customer_name', e.target.value)}
                    className={errors.customer_name ? 'border-red-500' : ''}
                />
                {errors.customer_name && (
                    <p className="mt-1 text-sm text-red-600">{errors.customer_name}</p>
                )}
            </div>

            <div>
                <label htmlFor="customer_email">Email</label>
                <input
                    id="customer_email"
                    type="email"
                    value={data.customer_email}
                    onChange={e => setData('customer_email', e.target.value)}
                    className={errors.customer_email ? 'border-red-500' : ''}
                />
                {errors.customer_email && (
                    <p className="mt-1 text-sm text-red-600">{errors.customer_email}</p>
                )}
            </div>

            <button type="submit" disabled={processing}>
                {processing ? 'Creating...' : 'Create Order'}
            </button>
        </form>
    );
}
```

### Reusable InputError Component

```tsx
// resources/js/Components/InputError.tsx
interface Props {
    message?: string;
    className?: string;
}

export default function InputError({ message, className = '' }: Props) {
    if (!message) return null;

    return (
        <p className={`text-sm text-red-600 ${className}`}>
            {message}
        </p>
    );
}

// Usage:
<InputError message={errors.customer_name} className="mt-1" />
```

---

## 5. Database Transaction Error Handling

### Wrapping Service Operations in Transactions

```php
use Illuminate\Support\Facades\DB;
use Illuminate\Database\QueryException;

class OrderService
{
    public function create(OrderDTO $dto): Order
    {
        try {
            return DB::transaction(function () use ($dto) {
                $order = Order::create($dto->toArray());

                foreach ($dto->items as $item) {
                    $order->items()->create($item->toArray());
                    $this->decrementInventory($item->productId, $item->quantity);
                }

                event(new OrderCreated($order));

                return $order->fresh(['items']);
            });
        } catch (QueryException $e) {
            // Handle specific database errors
            if ($e->getCode() === '23000') { // Integrity constraint violation
                throw new DomainException(
                    'A database constraint was violated. Please check your data.',
                    'constraint_violation',
                );
            }
            throw $e; // Re-throw unexpected database errors
        }
    }

    private function decrementInventory(int $productId, int $quantity): void
    {
        $affected = Product::where('id', $productId)
            ->where('stock', '>=', $quantity)
            ->decrement('stock', $quantity);

        if ($affected === 0) {
            $product = Product::find($productId);
            throw InsufficientInventoryException::forProduct(
                $productId,
                $quantity,
                $product->stock ?? 0,
            );
        }
    }
}
```

### Deadlock Retry

```php
use Illuminate\Support\Facades\DB;

// Laravel handles deadlock retries automatically with the attempts parameter:
DB::transaction(function () {
    // Critical section
    Order::lockForUpdate()->find($orderId);
    // ...
}, attempts: 3); // Retry up to 3 times on deadlock
```

---

## 6. React Error Boundaries

### Class Component Error Boundary

```tsx
// resources/js/Components/ErrorBoundary.tsx
import React, { Component, ErrorInfo, ReactNode } from 'react';

interface Props {
    children: ReactNode;
    fallback?: ReactNode;
    onError?: (error: Error, errorInfo: ErrorInfo) => void;
}

interface State {
    hasError: boolean;
    error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
    constructor(props: Props) {
        super(props);
        this.state = { hasError: false, error: null };
    }

    static getDerivedStateFromError(error: Error): State {
        return { hasError: true, error };
    }

    componentDidCatch(error: Error, errorInfo: ErrorInfo): void {
        console.error('ErrorBoundary caught:', error, errorInfo);

        // ~~monitoring: Report to Sentry/Bugsnag
        // Sentry.captureException(error, { extra: errorInfo });

        this.props.onError?.(error, errorInfo);
    }

    render() {
        if (this.state.hasError) {
            return this.props.fallback ?? (
                <div className="flex flex-col items-center justify-center min-h-[400px] p-8">
                    <h2 className="text-xl font-semibold text-gray-900">Something went wrong</h2>
                    <p className="mt-2 text-gray-600">
                        An unexpected error occurred. Please try refreshing the page.
                    </p>
                    <button
                        onClick={() => this.setState({ hasError: false, error: null })}
                        className="mt-4 px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
                    >
                        Try Again
                    </button>
                </div>
            );
        }

        return this.props.children;
    }
}
```

### Functional Wrapper Pattern

```tsx
// resources/js/hooks/useErrorHandler.ts
import { useCallback, useState } from 'react';

export function useErrorHandler() {
    const [error, setError] = useState<Error | null>(null);

    const handleError = useCallback((error: unknown) => {
        const normalizedError = error instanceof Error
            ? error
            : new Error(String(error));

        setError(normalizedError);

        // ~~monitoring: Report to external service
        console.error('Caught error:', normalizedError);
    }, []);

    const clearError = useCallback(() => setError(null), []);

    return { error, handleError, clearError };
}
```

### Using Error Boundaries in Layouts

```tsx
// resources/js/Layouts/AuthenticatedLayout.tsx
import { ErrorBoundary } from '@/Components/ErrorBoundary';

export default function AuthenticatedLayout({ children }: { children: React.ReactNode }) {
    return (
        <div className="min-h-screen bg-gray-100">
            <nav>{/* Navigation */}</nav>

            <ErrorBoundary
                fallback={
                    <div className="max-w-7xl mx-auto py-12 px-4 text-center">
                        <h2 className="text-2xl font-bold">Page Error</h2>
                        <p className="mt-2 text-gray-600">
                            This section encountered an error. Your data is safe.
                        </p>
                        <a href="/" className="mt-4 inline-block text-blue-600 hover:underline">
                            Return to Dashboard
                        </a>
                    </div>
                }
            >
                <main className="max-w-7xl mx-auto py-6 px-4">{children}</main>
            </ErrorBoundary>
        </div>
    );
}
```

---

## 7. Inertia.js Error Handling

### Inertia Error Pages

Configure Inertia to render custom error pages for HTTP errors:

```php
// bootstrap/app.php (Laravel 11+)
use Inertia\Inertia;
use Symfony\Component\HttpFoundation\Response;

->withExceptions(function (Exceptions $exceptions) {
    $exceptions->respond(function (Response $response) {
        $status = $response->getStatusCode();

        if (in_array($status, [403, 404, 500, 503])) {
            return Inertia::render('Error', [
                'status' => $status,
                'message' => match ($status) {
                    403 => 'You are not authorized to access this page.',
                    404 => 'The page you are looking for could not be found.',
                    500 => 'An unexpected server error occurred.',
                    503 => 'The service is temporarily unavailable.',
                },
            ])->toResponse(request())->setStatusCode($status);
        }

        return $response;
    });
})
```

### Error Page Component

```tsx
// resources/js/Pages/Error.tsx
import { Head } from '@inertiajs/react';

interface Props {
    status: number;
    message: string;
}

export default function Error({ status, message }: Props) {
    const titles: Record<number, string> = {
        403: 'Forbidden',
        404: 'Not Found',
        500: 'Server Error',
        503: 'Service Unavailable',
    };

    return (
        <>
            <Head title={`${status} - ${titles[status] ?? 'Error'}`} />
            <div className="flex min-h-screen items-center justify-center">
                <div className="text-center">
                    <h1 className="text-6xl font-bold text-gray-300">{status}</h1>
                    <h2 className="mt-4 text-2xl font-semibold text-gray-900">
                        {titles[status] ?? 'Error'}
                    </h2>
                    <p className="mt-2 text-gray-600">{message}</p>
                    <a
                        href="/"
                        className="mt-6 inline-block px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700"
                    >
                        Go Home
                    </a>
                </div>
            </div>
        </>
    );
}
```

### Global Inertia Error Listener

```tsx
// resources/js/app.tsx
import { router } from '@inertiajs/react';

// Listen for global Inertia errors (network failures, unexpected responses)
router.on('error', (event) => {
    console.error('Inertia error:', event);
    // ~~monitoring: Report to error tracking service
});

// Handle invalid responses (e.g., non-Inertia response from server)
router.on('invalid', (event) => {
    event.preventDefault();
    console.error('Invalid Inertia response. Reloading page.');
    window.location.reload();
});

// Handle exceptions during visit
router.on('exception', (event) => {
    event.preventDefault();
    console.error('Inertia exception:', event.detail.exception);
});
```

### Flash Error Display

```tsx
// resources/js/Components/FlashMessages.tsx
import { usePage } from '@inertiajs/react';
import { useEffect, useState } from 'react';

interface SharedProps {
    flash: {
        success?: string;
        error?: string;
    };
}

export default function FlashMessages() {
    const { flash } = usePage<SharedProps>().props;
    const [visible, setVisible] = useState(false);

    useEffect(() => {
        if (flash.success || flash.error) {
            setVisible(true);
            const timer = setTimeout(() => setVisible(false), 5000);
            return () => clearTimeout(timer);
        }
    }, [flash]);

    if (!visible || (!flash.success && !flash.error)) return null;

    return (
        <div className="fixed top-4 right-4 z-50">
            {flash.success && (
                <div className="rounded-lg bg-green-50 p-4 border border-green-200">
                    <p className="text-sm text-green-800">{flash.success}</p>
                </div>
            )}
            {flash.error && (
                <div className="rounded-lg bg-red-50 p-4 border border-red-200">
                    <p className="text-sm text-red-800">{flash.error}</p>
                </div>
            )}
        </div>
    );
}
```

---

## 8. Logging Strategies

### Channel Configuration

```php
// config/logging.php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['daily', 'slack'],
        'ignore_exceptions' => false,
    ],

    'daily' => [
        'driver' => 'daily',
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
        'days' => 14,
    ],

    'orders' => [
        'driver' => 'daily',
        'path' => storage_path('logs/orders.log'),
        'level' => 'info',
        'days' => 30,
    ],

    'slack' => [
        'driver' => 'slack',
        'url' => env('LOG_SLACK_WEBHOOK_URL'),
        'level' => 'critical',
        'username' => 'Laravel Log',
    ],

    'stderr' => [
        'driver' => 'monolog',
        'level' => 'debug',
        'handler' => StreamHandler::class,
        'with' => ['stream' => 'php://stderr'],
    ],
],
```

### Structured Logging with Context

```php
use Illuminate\Support\Facades\Log;

// Basic contextual logging
Log::info('Order created', [
    'order_id'  => $order->id,
    'user_id'   => $order->user_id,
    'total'     => $order->total,
]);

// Channel-specific logging
Log::channel('orders')->info('Order shipped', [
    'order_id'   => $order->id,
    'carrier'    => $carrier,
    'tracking'   => $trackingNumber,
]);

// Multi-channel logging
Log::stack(['daily', 'slack'])->critical('Payment gateway failure', [
    'gateway'    => 'stripe',
    'order_id'   => $order->id,
    'error'      => $exception->getMessage(),
]);

// Log with exception context
try {
    $this->processPayment($order);
} catch (PaymentFailedException $e) {
    Log::error('Payment processing failed', [
        'order_id'   => $order->id,
        'error_code' => $e->errorCode,
        'context'    => $e->context,
        'exception'  => $e,
    ]);
    throw $e;
}
```

### Adding Global Log Context

```php
// In a middleware or ServiceProvider:
use Illuminate\Support\Facades\Log;

Log::shareContext([
    'request_id' => request()->header('X-Request-ID', (string) Str::uuid()),
    'user_id'    => auth()->id(),
    'ip'         => request()->ip(),
]);
```

---

## 9. Error Monitoring Integration

### Sentry/Bugsnag Placeholder Pattern

```php
// ~~monitoring: Replace these placeholders with your monitoring service

// In bootstrap/app.php:
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->report(function (Throwable $e) {
        // ~~monitoring: Sentry integration
        // if (app()->bound('sentry')) {
        //     app('sentry')->captureException($e);
        // }

        // ~~monitoring: Bugsnag integration
        // if (app()->bound('bugsnag')) {
        //     app('bugsnag')->notifyException($e);
        // }
    });
})
```

```tsx
// ~~monitoring: Frontend error tracking in React ErrorBoundary
componentDidCatch(error: Error, errorInfo: ErrorInfo): void {
    // ~~monitoring: Sentry browser integration
    // Sentry.captureException(error, { extra: { componentStack: errorInfo.componentStack } });

    // ~~monitoring: Bugsnag browser integration
    // Bugsnag.notify(error, (event) => {
    //     event.addMetadata('react', { componentStack: errorInfo.componentStack });
    // });
}
```

---

## 10. Testing Error Scenarios

### Testing Domain Exception Throwing

```php
use App\Exceptions\OrderNotCancellableException;
use App\Enums\OrderStatus;
use App\Models\Order;

it('throws when cancelling a shipped order', function () {
    $order = Order::factory()->shipped()->create();

    $this->service->cancel($order);
})->throws(OrderNotCancellableException::class);

it('includes order id in exception', function () {
    $order = Order::factory()->shipped()->create();

    try {
        $this->service->cancel($order);
        $this->fail('Expected exception was not thrown.');
    } catch (OrderNotCancellableException $e) {
        expect($e->orderId)->toBe($order->id);
        expect($e->getMessage())->toContain((string) $order->id);
    }
});
```

### Testing API Error Responses

```php
it('returns 404 for nonexistent order', function () {
    $this->actingAs(User::factory()->create())
        ->getJson('/api/orders/99999')
        ->assertNotFound()
        ->assertJson([
            'error'   => 'resource_not_found',
            'message' => 'The requested resource was not found.',
        ]);
});

it('returns 422 with validation errors', function () {
    $this->actingAs(User::factory()->create())
        ->postJson('/api/orders', [])
        ->assertUnprocessable()
        ->assertJsonValidationErrors(['customer_name', 'items']);
});

it('returns 403 for unauthorized access', function () {
    $order = Order::factory()->create();

    $this->actingAs(User::factory()->create())
        ->getJson("/api/orders/{$order->id}")
        ->assertForbidden();
});
```

### Testing Transaction Rollback

```php
it('rolls back on inventory failure', function () {
    $product = Product::factory()->create(['stock' => 1]);

    $dto = new OrderDTO(
        // ...order data with quantity: 10 (more than available)
    );

    expect(fn () => $this->service->create($dto))
        ->toThrow(InsufficientInventoryException::class);

    // Verify no order was created (transaction rolled back)
    $this->assertDatabaseCount('orders', 0);

    // Verify stock was not decremented
    expect($product->fresh()->stock)->toBe(1);
});
```

### Testing React Error Boundary

```tsx
import { render, screen } from '@testing-library/react';
import { ErrorBoundary } from '@/Components/ErrorBoundary';

const ThrowingComponent = () => {
    throw new Error('Test error');
};

it('renders fallback on error', () => {
    const consoleSpy = vi.spyOn(console, 'error').mockImplementation(() => {});

    render(
        <ErrorBoundary fallback={<div>Something went wrong</div>}>
            <ThrowingComponent />
        </ErrorBoundary>
    );

    expect(screen.getByText('Something went wrong')).toBeInTheDocument();
    consoleSpy.mockRestore();
});

it('calls onError callback', () => {
    const consoleSpy = vi.spyOn(console, 'error').mockImplementation(() => {});
    const onError = vi.fn();

    render(
        <ErrorBoundary onError={onError}>
            <ThrowingComponent />
        </ErrorBoundary>
    );

    expect(onError).toHaveBeenCalledWith(
        expect.any(Error),
        expect.objectContaining({ componentStack: expect.any(String) })
    );
    consoleSpy.mockRestore();
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bramato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
