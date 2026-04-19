---
name: order-processing
description: Complete guide for implementing order lifecycle management in Sabai Use when this capability is needed.
metadata:
  author: saidabbos
---

# Order Processing Skill

This skill covers implementing order management features for the Sabai massage booking system.

## When to Use This Skill

- Creating new order-related features
- Implementing order status transitions
- Building order management admin pages
- Creating order APIs for client/cabinet

---

## Order Model Setup

### Migration

```php
// database/migrations/xxxx_create_orders_table.php

Schema::create('orders', function (Blueprint $table) {
    $table->id();
    $table->foreignId('client_id')->constrained()->onDelete('cascade');
    $table->foreignId('therapist_id')->constrained()->onDelete('cascade');
    $table->foreignId('slot_id')->constrained()->onDelete('cascade');
    $table->foreignId('massage_type_id')->constrained()->onDelete('cascade');
    $table->foreignId('oil_type_id')->nullable()->constrained()->onDelete('set null');

    $table->string('public_token', 64)->unique();

    // Status fields
    $table->string('status')->default('NEW');
    $table->string('payment_status')->default('NOT_INVOICED');

    // Payment fields
    $table->integer('total_amount'); // in UZS
    $table->string('pay_provider')->nullable(); // payme, click
    $table->string('pay_reference')->nullable();
    $table->string('pay_url')->nullable();
    $table->timestamp('pay_expires_at')->nullable();
    $table->timestamp('paid_at')->nullable();

    // Work order fields
    $table->string('work_order_status')->default('NONE'); // NONE, READY, SENT
    $table->text('work_order_text')->nullable();
    $table->timestamp('sent_to_therapist_at')->nullable();
    $table->timestamp('ready_sent_at')->nullable();

    $table->timestamps();

    $table->index(['status', 'payment_status']);
    $table->index('created_at');
});
```

### Model

```php
// app/Models/Order.php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasOne;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Order extends Model
{
    use HasFactory;

    // Order statuses
    const STATUS_NEW = 'NEW';
    const STATUS_CONFIRMING = 'CONFIRMING';
    const STATUS_WAITING_PAYMENT = 'WAITING_PAYMENT';
    const STATUS_PAID = 'PAID';
    const STATUS_RESERVED = 'RESERVED';
    const STATUS_COMPLETED = 'COMPLETED';
    const STATUS_CANCELLED = 'CANCELLED';
    const STATUS_CANCEL_REQUESTED = 'CANCEL_REQUESTED';

    // Payment statuses
    const PAY_NOT_INVOICED = 'NOT_INVOICED';
    const PAY_INVOICED = 'INVOICED';
    const PAY_PAID = 'PAID';
    const PAY_FAILED = 'FAILED';
    const PAY_REFUNDED = 'REFUNDED';

    // Work order statuses
    const WO_NONE = 'NONE';
    const WO_READY = 'READY';
    const WO_SENT = 'SENT';

    protected $fillable = [
        'client_id',
        'therapist_id',
        'slot_id',
        'massage_type_id',
        'oil_type_id',
        'public_token',
        'status',
        'payment_status',
        'total_amount',
        'pay_provider',
        'pay_reference',
        'pay_url',
        'pay_expires_at',
        'paid_at',
        'work_order_status',
        'work_order_text',
        'sent_to_therapist_at',
        'ready_sent_at',
    ];

    protected $casts = [
        'total_amount' => 'integer',
        'pay_expires_at' => 'datetime',
        'paid_at' => 'datetime',
        'sent_to_therapist_at' => 'datetime',
        'ready_sent_at' => 'datetime',
    ];

    // Relationships
    public function client(): BelongsTo
    {
        return $this->belongsTo(Client::class);
    }

    public function therapist(): BelongsTo
    {
        return $this->belongsTo(Therapist::class);
    }

    public function slot(): BelongsTo
    {
        return $this->belongsTo(Slot::class);
    }

    public function massageType(): BelongsTo
    {
        return $this->belongsTo(MassageType::class);
    }

    public function oilType(): BelongsTo
    {
        return $this->belongsTo(OilType::class);
    }

    public function confirmation(): HasOne
    {
        return $this->hasOne(OrderConfirmation::class);
    }

    public function quality(): HasOne
    {
        return $this->hasOne(OrderQuality::class);
    }

    public function auditLogs(): HasMany
    {
        return $this->hasMany(OrderAuditLog::class)->latest();
    }

    // Helpers
    public function isPaid(): bool
    {
        return $this->payment_status === self::PAY_PAID;
    }

    public function isReserved(): bool
    {
        return $this->status === self::STATUS_RESERVED;
    }

    public function canGenerateWorkOrder(): bool
    {
        return $this->isPaid() && $this->isReserved();
    }

    public function canComplete(): bool
    {
        return $this->isReserved() && $this->quality !== null;
    }

    public function getFormattedAmountAttribute(): string
    {
        return number_format($this->total_amount, 0, '', ' ') . ' UZS';
    }

    // Audit logging
    public function logEvent(string $event, array $data = []): OrderAuditLog
    {
        return $this->auditLogs()->create([
            'event' => $event,
            'user_id' => auth()->id(),
            'data' => $data,
        ]);
    }

    // Boot
    protected static function boot()
    {
        parent::boot();

        static::creating(function ($order) {
            if (empty($order->public_token)) {
                $order->public_token = \Str::random(32);
            }
        });
    }
}
```

---

## Related Models

### OrderConfirmation

```php
// app/Models/OrderConfirmation.php

class OrderConfirmation extends Model
{
    const OUTCOME_CONFIRMED = 'CONFIRMED';
    const OUTCOME_RESCHEDULE = 'RESCHEDULE';
    const OUTCOME_NO_ANSWER = 'NO_ANSWER';
    const OUTCOME_CANCELLED = 'CANCELLED';

    protected $primaryKey = 'order_id';
    public $incrementing = false;

    protected $fillable = [
        'order_id',
        'address',
        'entrance',
        'floor',
        'elevator',
        'parking',
        'landmark',
        'onsite_phone',
        'constraints',
        'space_ok',
        'pets',
        'note_to_therapist',
        'call_outcome',
        'filled_by',
        'filled_at',
    ];

    protected $casts = [
        'elevator' => 'boolean',
        'space_ok' => 'boolean',
        'pets' => 'boolean',
        'filled_at' => 'datetime',
    ];

    public function order(): BelongsTo
    {
        return $this->belongsTo(Order::class);
    }

    public function filledBy(): BelongsTo
    {
        return $this->belongsTo(User::class, 'filled_by');
    }
}
```

### OrderQuality

```php
// app/Models/OrderQuality.php

class OrderQuality extends Model
{
    protected $primaryKey = 'order_id';
    public $incrementing = false;

    protected $fillable = [
        'order_id',
        'rating_punctuality',
        'rating_quality',
        'rating_communication',
        'rating_overall',
        'will_order_again',
        'recommend',
        'hygiene_issue',
        'hygiene_comment',
        'improvement_comment',
        'filled_by',
        'filled_at',
    ];

    protected $casts = [
        'rating_punctuality' => 'integer',
        'rating_quality' => 'integer',
        'rating_communication' => 'integer',
        'rating_overall' => 'integer',
        'will_order_again' => 'boolean',
        'recommend' => 'boolean',
        'hygiene_issue' => 'boolean',
        'filled_at' => 'datetime',
    ];

    public function order(): BelongsTo
    {
        return $this->belongsTo(Order::class);
    }

    public function filledBy(): BelongsTo
    {
        return $this->belongsTo(User::class, 'filled_by');
    }
}
```

### OrderAuditLog

```php
// app/Models/OrderAuditLog.php

class OrderAuditLog extends Model
{
    public $timestamps = false;

    protected $fillable = [
        'order_id',
        'event',
        'user_id',
        'data',
    ];

    protected $casts = [
        'data' => 'array',
        'created_at' => 'datetime',
    ];

    protected static function boot()
    {
        parent::boot();
        static::creating(fn($log) => $log->created_at = now());
    }

    public function order(): BelongsTo
    {
        return $this->belongsTo(Order::class);
    }

    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}
```

---

## Service Implementation

```php
// app/Services/OrderService.php

namespace App\Services;

use App\Models\{Order, Client, Slot, OrderConfirmation, OrderQuality};
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Str;

class OrderService
{
    public function __construct(
        private SlotService $slotService,
        private PaymentService $paymentService,
        private TelegramService $telegram
    ) {}

    /**
     * Create order from public booking form
     */
    public function createFromBooking(array $data): Order
    {
        return DB::transaction(function () use ($data) {
            // Lock slot
            $slot = Slot::lockForUpdate()->findOrFail($data['slot_id']);

            if (!$slot->canBook()) {
                throw new \App\Exceptions\SlotNotAvailableException();
            }

            // Get or create client
            $client = Client::firstOrCreate(
                ['phone' => $this->normalizePhone($data['phone'])],
                [
                    'name' => $data['name'] ?? null,
                    'comment' => $data['comment'] ?? null,
                ]
            );

            // Create order
            $order = Order::create([
                'client_id' => $client->id,
                'therapist_id' => $slot->therapist_id,
                'slot_id' => $slot->id,
                'massage_type_id' => $data['massage_type_id'],
                'oil_type_id' => $data['oil_type_id'] ?? null,
                'total_amount' => config('booking.default_price', 500000),
                'status' => Order::STATUS_NEW,
                'payment_status' => Order::PAY_NOT_INVOICED,
            ]);

            // Mark slot as pending
            $this->slotService->markPending($slot, $order);

            // Log and notify
            $order->logEvent('created');
            $this->telegram->sendNew($order);

            return $order->load(['client', 'therapist', 'slot', 'massageType']);
        });
    }

    /**
     * Normalize phone number to +998 format
     */
    private function normalizePhone(string $phone): string
    {
        $digits = preg_replace('/\D/', '', $phone);

        if (strlen($digits) === 9) {
            return '+998' . $digits;
        }

        if (strlen($digits) === 12 && str_starts_with($digits, '998')) {
            return '+' . $digits;
        }

        return '+' . $digits;
    }

    /**
     * Update order status with validation
     */
    public function updateStatus(Order $order, string $newStatus): void
    {
        $validTransitions = [
            Order::STATUS_NEW => [Order::STATUS_CONFIRMING, Order::STATUS_CANCELLED],
            Order::STATUS_CONFIRMING => [Order::STATUS_WAITING_PAYMENT, Order::STATUS_CANCELLED],
            Order::STATUS_WAITING_PAYMENT => [Order::STATUS_PAID, Order::STATUS_CANCELLED],
            Order::STATUS_PAID => [Order::STATUS_RESERVED, Order::STATUS_CANCELLED],
            Order::STATUS_RESERVED => [Order::STATUS_COMPLETED, Order::STATUS_CANCEL_REQUESTED],
            Order::STATUS_CANCEL_REQUESTED => [Order::STATUS_RESERVED, Order::STATUS_CANCELLED],
        ];

        if (!isset($validTransitions[$order->status]) ||
            !in_array($newStatus, $validTransitions[$order->status])) {
            throw new \App\Exceptions\InvalidStatusTransitionException(
                "Cannot transition from {$order->status} to {$newStatus}"
            );
        }

        $oldStatus = $order->status;
        $order->update(['status' => $newStatus]);
        $order->logEvent('status_changed', [
            'from' => $oldStatus,
            'to' => $newStatus,
        ]);
    }
}
```

---

## API Endpoints

### Public Booking API

```php
// routes/api.php

Route::prefix('booking')->group(function () {
    Route::get('/therapists', [BookingController::class, 'therapists']);
    Route::get('/therapists/{therapist}/slots', [BookingController::class, 'slots']);
    Route::get('/massage-types', [BookingController::class, 'massageTypes']);
    Route::get('/oil-types', [BookingController::class, 'oilTypes']);
    Route::post('/orders', [BookingController::class, 'store']);
});

// app/Http/Controllers/Api/BookingController.php

class BookingController extends Controller
{
    public function __construct(private OrderService $orderService) {}

    public function therapists()
    {
        $therapists = Therapist::where('is_active', true)
            ->select(['id', 'name', 'photo', 'rating'])
            ->get();

        return response()->json(['therapists' => $therapists]);
    }

    public function slots(Therapist $therapist, Request $request)
    {
        $date = $request->get('date', now()->toDateString());

        $slots = Slot::where('therapist_id', $therapist->id)
            ->whereDate('date', $date)
            ->orderBy('start_time')
            ->get()
            ->map(fn($slot) => [
                'id' => $slot->id,
                'time' => substr($slot->start_time, 0, 5),
                'status' => $slot->status,
                'bookable' => $slot->canBook(),
            ]);

        return response()->json(['slots' => $slots]);
    }

    public function store(Request $request)
    {
        $validated = $request->validate([
            'slot_id' => 'required|exists:slots,id',
            'massage_type_id' => 'required|exists:massage_types,id',
            'oil_type_id' => 'nullable|exists:oil_types,id',
            'phone' => 'required|string|regex:/^\+?998\d{9}$/',
            'name' => 'nullable|string|max:100',
            'comment' => 'nullable|string|max:300',
            'privacy_consent' => 'required|accepted',
        ]);

        try {
            $order = $this->orderService->createFromBooking($validated);

            return response()->json([
                'success' => true,
                'message' => 'Request received. Dispatcher will confirm shortly.',
                'order_id' => $order->id,
            ], 201);

        } catch (\App\Exceptions\SlotNotAvailableException $e) {
            return response()->json([
                'success' => false,
                'message' => 'This time is unavailable. Please choose another slot.',
            ], 422);
        }
    }
}
```

---

## Vue Components

### Order Card (Admin)

```vue
<!-- resources/js/Pages/Admin/Orders/Show.vue -->

<script setup>
import { ref } from 'vue'
import { useForm, router } from '@inertiajs/vue3'
import AdminLayout from '@/Layouts/AdminLayout.vue'

const props = defineProps({
    order: Object,
})

// Confirmation form
const confirmationForm = useForm({
    address: props.order.confirmation?.address ?? '',
    entrance: props.order.confirmation?.entrance ?? '',
    floor: props.order.confirmation?.floor ?? '',
    elevator: props.order.confirmation?.elevator ?? false,
    parking: props.order.confirmation?.parking ?? '',
    landmark: props.order.confirmation?.landmark ?? '',
    onsite_phone: props.order.confirmation?.onsite_phone ?? '',
    constraints: props.order.confirmation?.constraints ?? '',
    space_ok: props.order.confirmation?.space_ok ?? false,
    pets: props.order.confirmation?.pets ?? false,
    note_to_therapist: props.order.confirmation?.note_to_therapist ?? '',
    call_outcome: props.order.confirmation?.call_outcome ?? '',
})

const saveConfirmation = () => {
    confirmationForm.post(route('admin.orders.confirmation', props.order.id))
}

// Payment actions
const createInvoice = (provider) => {
    router.post(route('admin.orders.invoice', props.order.id), { provider })
}

const confirmBooking = () => {
    router.post(route('admin.orders.confirm-booking', props.order.id))
}
</script>

<template>
    <AdminLayout>
        <div class="order-card">
            <!-- Block A: Client -->
            <section class="card">
                <h3>Client</h3>
                <p><strong>Phone:</strong>
                    <a :href="'tel:' + order.client.phone">{{ order.client.phone }}</a>
                </p>
                <p><strong>Name:</strong> {{ order.client.name || '—' }}</p>
            </section>

            <!-- Block B: Order Details -->
            <section class="card">
                <h3>Order Details</h3>
                <p><strong>Massage:</strong> {{ order.massage_type.name }}</p>
                <p v-if="order.oil_type"><strong>Oil:</strong> {{ order.oil_type.name }}</p>
                <p><strong>Therapist:</strong> {{ order.therapist.name }}</p>
                <p><strong>Date/Time:</strong> {{ order.slot.date }} {{ order.slot.start_time }}</p>
                <p><strong>Status:</strong> {{ order.status }}</p>
                <p><strong>Payment:</strong> {{ order.payment_status }}</p>
            </section>

            <!-- Block C: Confirmation Form -->
            <section class="card">
                <h3>Confirmation Form</h3>
                <form @submit.prevent="saveConfirmation">
                    <div class="form-group">
                        <label>Address *</label>
                        <input v-model="confirmationForm.address" required />
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label>Entrance</label>
                            <input v-model="confirmationForm.entrance" />
                        </div>
                        <div class="form-group">
                            <label>Floor</label>
                            <input v-model="confirmationForm.floor" />
                        </div>
                    </div>
                    <div class="form-group">
                        <label>
                            <input type="checkbox" v-model="confirmationForm.elevator" />
                            Elevator available
                        </label>
                    </div>
                    <div class="form-group">
                        <label>Call Outcome *</label>
                        <select v-model="confirmationForm.call_outcome" required>
                            <option value="">Select...</option>
                            <option value="CONFIRMED">Confirmed</option>
                            <option value="RESCHEDULE">Reschedule</option>
                            <option value="NO_ANSWER">No Answer</option>
                            <option value="CANCELLED">Cancelled</option>
                        </select>
                    </div>
                    <button type="submit" :disabled="confirmationForm.processing">
                        Save Confirmation
                    </button>
                </form>
            </section>

            <!-- Block D: Payment -->
            <section class="card" v-if="order.confirmation?.call_outcome === 'CONFIRMED'">
                <h3>Payment</h3>
                <template v-if="order.payment_status === 'NOT_INVOICED'">
                    <button @click="createInvoice('payme')">Create Payme Invoice</button>
                    <button @click="createInvoice('click')">Create Click Invoice</button>
                </template>
                <template v-else-if="order.payment_status === 'INVOICED'">
                    <p>Invoice sent. Waiting for payment...</p>
                    <p><a :href="order.pay_url" target="_blank">Payment Link</a></p>
                </template>
                <template v-else-if="order.payment_status === 'PAID'">
                    <p class="success">Payment confirmed</p>
                </template>
            </section>

            <!-- Block E: Booking -->
            <section class="card" v-if="order.payment_status === 'PAID' && order.status !== 'RESERVED'">
                <h3>Booking</h3>
                <button @click="confirmBooking">Confirm Booking (Reserve Slot)</button>
            </section>
        </div>
    </AdminLayout>
</template>
```

---

## Testing

```php
// tests/Feature/OrderTest.php

class OrderTest extends TestCase
{
    use RefreshDatabase;

    public function test_client_can_create_order()
    {
        $therapist = Therapist::factory()->create();
        $slot = Slot::factory()->free()->create([
            'therapist_id' => $therapist->id,
            'date' => now()->addDay()->toDateString(),
        ]);
        $massageType = MassageType::factory()->create();

        $response = $this->postJson('/api/booking/orders', [
            'slot_id' => $slot->id,
            'massage_type_id' => $massageType->id,
            'phone' => '+998901234567',
            'privacy_consent' => true,
        ]);

        $response->assertStatus(201);
        $this->assertDatabaseHas('orders', [
            'slot_id' => $slot->id,
            'status' => 'NEW',
        ]);
        $this->assertEquals('PENDING', $slot->fresh()->status);
    }

    public function test_cannot_book_taken_slot()
    {
        $slot = Slot::factory()->pending()->create();
        $massageType = MassageType::factory()->create();

        $response = $this->postJson('/api/booking/orders', [
            'slot_id' => $slot->id,
            'massage_type_id' => $massageType->id,
            'phone' => '+998901234567',
            'privacy_consent' => true,
        ]);

        $response->assertStatus(422);
    }

    public function test_dispatcher_can_complete_order_flow()
    {
        $dispatcher = User::factory()->create();
        $dispatcher->assignRole('dispatcher');

        $order = Order::factory()->create(['status' => 'NEW']);

        // Fill confirmation
        $this->actingAs($dispatcher)
            ->post(route('admin.orders.confirmation', $order), [
                'address' => 'Test Address',
                'call_outcome' => 'CONFIRMED',
            ])
            ->assertRedirect();

        $this->assertEquals('CONFIRMING', $order->fresh()->status);
    }
}
```

---

## Checklist

- [ ] Orders table migration created
- [ ] Order model with constants and relationships
- [ ] OrderConfirmation model
- [ ] OrderQuality model
- [ ] OrderAuditLog model
- [ ] OrderService with all business logic
- [ ] Double-booking prevention with DB locks
- [ ] Booking API endpoints
- [ ] Admin order management pages
- [ ] Status transition validation
- [ ] Audit logging for all actions
- [ ] Feature tests for critical flows

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/saidabbos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
