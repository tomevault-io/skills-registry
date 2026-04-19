---
name: telegram-notifications
description: Guide for implementing Telegram bot notifications for Sabai Use when this capability is needed.
metadata:
  author: saidabbos
---

# Telegram Notifications Skill

This skill covers implementing Telegram notifications for the Sabai booking system.

## When to Use This Skill

- Setting up Telegram bots
- Implementing notification messages
- Sending order updates to groups
- Sending work orders to therapists

---

## Architecture Overview

Sabai uses **2 bots** and **2 groups**:

| Bot | Group | Events | Audience |
|-----|-------|--------|----------|
| OPS Bot | OPS Group | NEW, PAID | Owner, Dispatcher |
| Therapist Bot | Therapists Group | READY | All Therapists, Owner, Dispatcher |

This separation ensures:
- Masters don't see raw NEW orders (prevents confusion)
- Masters only get complete, paid orders
- Different bot tokens prevent accidental cross-posting

---

## Configuration

### Environment Variables

```env
# OPS Bot (for Owner/Dispatcher)
OPS_BOT_TOKEN=1234567890:ABCdefGHIjklMNOpqrSTUvwxYZ
OPS_GROUP_CHAT_ID=-1001234567890

# Therapist Bot (for Masters)
THERAPIST_BOT_TOKEN=0987654321:ZYXwvuTSRqpONMlkjIHGfedCBA
THERAPIST_GROUP_CHAT_ID=-1009876543210

# Base URL for links
APP_URL=https://sabai.uz
```

### Config File

```php
// config/telegram.php

return [
    'ops' => [
        'token' => env('OPS_BOT_TOKEN'),
        'chat_id' => env('OPS_GROUP_CHAT_ID'),
    ],

    'therapist' => [
        'token' => env('THERAPIST_BOT_TOKEN'),
        'chat_id' => env('THERAPIST_GROUP_CHAT_ID'),
    ],

    'enabled' => env('TELEGRAM_ENABLED', true),

    'api_url' => 'https://api.telegram.org/bot',
];
```

---

## Telegram Service

```php
// app/Services/TelegramService.php

namespace App\Services;

use App\Models\Order;
use App\Models\Therapist;
use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Log;

class TelegramService
{
    /**
     * Send NEW order notification to OPS group
     */
    public function sendNew(Order $order): bool
    {
        if (!config('telegram.enabled')) {
            return false;
        }

        $order->load(['client', 'therapist', 'slot', 'massageType', 'oilType']);

        $oilShort = $order->oilType ? " ({$order->oilType->name})" : '';

        $message = "🆕 *NEW* | #{$order->id}\n\n";
        $message .= "{$order->massageType->name}{$oilShort} · 60 min · {$order->formatted_amount}\n";
        $message .= "Master: {$order->therapist->name}\n";
        $message .= "Time: {$order->slot->date} {$order->slot->start_time}\n";
        $message .= "Phone: `{$order->client->phone}`\n\n";
        $message .= "[Open in Admin]({$this->adminOrderUrl($order)})";

        return $this->sendToOps($message);
    }

    /**
     * Send PAID notification to OPS group
     */
    public function sendPaid(Order $order): bool
    {
        if (!config('telegram.enabled')) {
            return false;
        }

        $order->load(['therapist', 'slot']);

        $message = "✅ *PAID* | #{$order->id}\n\n";
        $message .= "{$order->formatted_amount} · {$order->pay_provider}\n";
        $message .= "Master: {$order->therapist->name} · {$order->slot->date} {$order->slot->start_time}\n\n";
        $message .= "[Open in Admin]({$this->adminOrderUrl($order)})";

        return $this->sendToOps($message);
    }

    /**
     * Send READY notification to Therapists group
     * Only sent when: CONFIRMED + PAID + RESERVED
     */
    public function sendReady(Order $order): bool
    {
        if (!config('telegram.enabled')) {
            return false;
        }

        // Verify conditions
        if (!$this->canSendReady($order)) {
            Log::warning("Attempted to send READY for order #{$order->id} but conditions not met");
            return false;
        }

        $order->load([
            'client', 'therapist', 'slot', 'massageType', 'oilType', 'confirmation'
        ]);

        $conf = $order->confirmation;
        $oilShort = $order->oilType ? " ({$order->oilType->name})" : '';

        $message = "✅ *READY* | #{$order->id}\n\n";

        // Basic info
        $message .= "👨‍⚕️ *Master:* {$order->therapist->name}\n";
        $message .= "🕐 *Time:* {$order->slot->date} {$order->slot->start_time} (60 min)\n";
        $message .= "💆 *Massage:* {$order->massageType->name}{$oilShort}\n\n";

        // Client info
        $message .= "📱 *Client*\n";
        $message .= "Phone: `{$order->client->phone}`\n";
        $message .= "Name: {$order->client->name ?: '—'}\n";
        $message .= "On-site phone: {$conf->onsite_phone ?: '—'}\n\n";

        // Address
        $message .= "📍 *Address*\n";
        $message .= "{$conf->address}\n";
        $elevatorText = $conf->elevator ? 'Yes' : 'No';
        $message .= "Entrance: {$conf->entrance ?: '—'} · Floor: {$conf->floor ?: '—'} · Elevator: {$elevatorText}\n";
        $message .= "Parking: {$conf->parking ?: '—'}\n";
        $message .= "Landmark: {$conf->landmark ?: '—'}\n\n";

        // Notes
        $message .= "📝 *Notes / Restrictions*\n";
        $message .= "Constraints: {$conf->constraints ?: '—'}\n";
        $message .= "Note to therapist: {$conf->note_to_therapist ?: '—'}\n";
        $spaceText = $conf->space_ok ? 'Yes' : 'No';
        $petsText = $conf->pets ? 'Yes' : 'No';
        $message .= "Space 2×2: {$spaceText} · Pets: {$petsText}\n\n";

        // Payment
        $message .= "💳 *Payment*\n";
        $message .= "✅ PAID · {$order->formatted_amount} · {$order->pay_provider}\n\n";

        // Links
        $message .= "🔗 *Links:*\n";
        $message .= "[My Day]({$this->masterDayUrl($order)})\n";
        $message .= "[Order Details]({$this->publicOrderUrl($order)})";

        return $this->sendToTherapists($message);
    }

    /**
     * Check if READY notification can be sent
     */
    private function canSendReady(Order $order): bool
    {
        // Must be RESERVED status
        if ($order->status !== Order::STATUS_RESERVED) {
            return false;
        }

        // Must be PAID
        if ($order->payment_status !== Order::PAY_PAID) {
            return false;
        }

        // Must have confirmation with CONFIRMED outcome
        if (!$order->confirmation || $order->confirmation->call_outcome !== 'CONFIRMED') {
            return false;
        }

        // Must have address filled
        if (empty($order->confirmation->address)) {
            return false;
        }

        // Must not be already sent
        if ($order->ready_sent_at) {
            return false;
        }

        return true;
    }

    /**
     * Send work order text directly to therapist
     */
    public function sendToTherapist(Therapist $therapist, string $text): bool
    {
        if (!$therapist->telegram_chat_id) {
            Log::warning("Therapist #{$therapist->id} has no telegram_chat_id");
            return false;
        }

        return $this->sendMessage(
            config('telegram.therapist.token'),
            $therapist->telegram_chat_id,
            $text
        );
    }

    /**
     * Resend READY notification (manual trigger from admin)
     */
    public function resendReady(Order $order): bool
    {
        // Temporarily clear ready_sent_at to allow resend
        $originalSentAt = $order->ready_sent_at;

        // Force refresh conditions check with original data
        if ($order->status !== Order::STATUS_RESERVED ||
            $order->payment_status !== Order::PAY_PAID) {
            return false;
        }

        // Temporarily allow
        $order->ready_sent_at = null;
        $result = $this->sendReady($order);

        // Restore if failed
        if (!$result) {
            $order->ready_sent_at = $originalSentAt;
        }

        return $result;
    }

    /**
     * Send message to OPS group
     */
    private function sendToOps(string $message): bool
    {
        return $this->sendMessage(
            config('telegram.ops.token'),
            config('telegram.ops.chat_id'),
            $message
        );
    }

    /**
     * Send message to Therapists group
     */
    private function sendToTherapists(string $message): bool
    {
        return $this->sendMessage(
            config('telegram.therapist.token'),
            config('telegram.therapist.chat_id'),
            $message
        );
    }

    /**
     * Send message via Telegram API
     */
    private function sendMessage(string $token, string $chatId, string $text): bool
    {
        try {
            $response = Http::timeout(10)
                ->post(config('telegram.api_url') . $token . '/sendMessage', [
                    'chat_id' => $chatId,
                    'text' => $text,
                    'parse_mode' => 'Markdown',
                    'disable_web_page_preview' => true,
                ]);

            if (!$response->successful()) {
                Log::error('Telegram API error', [
                    'status' => $response->status(),
                    'body' => $response->body(),
                ]);
                return false;
            }

            return true;

        } catch (\Exception $e) {
            Log::error('Telegram send failed', [
                'error' => $e->getMessage(),
            ]);
            return false;
        }
    }

    /**
     * Generate admin order URL
     */
    private function adminOrderUrl(Order $order): string
    {
        return config('app.url') . "/admin/orders/{$order->id}";
    }

    /**
     * Generate master day view URL
     */
    private function masterDayUrl(Order $order): string
    {
        $date = $order->slot->date;
        return config('app.url') . "/m/{$order->therapist->public_token}/day/{$date}";
    }

    /**
     * Generate public order URL
     */
    private function publicOrderUrl(Order $order): string
    {
        return config('app.url') . "/o/{$order->public_token}";
    }
}
```

---

## Message Templates

### NEW (OPS Group)

```
🆕 *NEW* | #123

Traditional Thai · 60 min · 500 000 UZS
Master: Anvar
Time: 2024-01-15 14:00
Phone: `+998901234567`

[Open in Admin](https://sabai.uz/admin/orders/123)
```

### PAID (OPS Group)

```
✅ *PAID* | #123

500 000 UZS · payme
Master: Anvar · 2024-01-15 14:00

[Open in Admin](https://sabai.uz/admin/orders/123)
```

### READY (Therapists Group)

```
✅ *READY* | #123

👨‍⚕️ *Master:* Anvar
🕐 *Time:* 2024-01-15 14:00 (60 min)
💆 *Massage:* Relax Oil Massage (Lavender oil)

📱 *Client*
Phone: `+998901234567`
Name: Sardor
On-site phone: +998909876543

📍 *Address*
Mirzo Ulugbek, Buyuk Ipak Yoli 123
Entrance: 2 · Floor: 5 · Elevator: Yes
Parking: Near building entrance
Landmark: Opposite to Makro

📝 *Notes / Restrictions*
Constraints: Lower back pain
Note to therapist: Extra focus on shoulders
Space 2×2: Yes · Pets: No

💳 *Payment*
✅ PAID · 500 000 UZS · payme

🔗 *Links:*
[My Day](https://sabai.uz/m/abc123token/day/2024-01-15)
[Order Details](https://sabai.uz/o/xyz789token)
```

---

## Integration Points

### In OrderService

```php
// app/Services/OrderService.php

public function create(array $data): Order
{
    return DB::transaction(function () use ($data) {
        // ... create order logic ...

        // Send NEW notification
        app(TelegramService::class)->sendNew($order);

        return $order;
    });
}

public function confirmPayment(Order $order, array $webhookData): void
{
    DB::transaction(function () use ($order, $webhookData) {
        // ... update payment status ...

        // Send PAID notification
        app(TelegramService::class)->sendPaid($order);

        // Auto-reserve and send READY if applicable
        if ($this->shouldAutoReserve($order)) {
            $this->confirmBooking($order);
        }
    });
}

public function confirmBooking(Order $order): void
{
    DB::transaction(function () use ($order) {
        // ... reserve slot ...

        // Try to send READY notification
        $telegram = app(TelegramService::class);
        if ($telegram->sendReady($order)) {
            $order->update(['ready_sent_at' => now()]);
            $order->logEvent('ready_sent');
        }
    });
}
```

---

## Admin Resend Feature

### Controller

```php
// app/Http/Controllers/Admin/OrderController.php

public function resendReady(Order $order)
{
    $this->authorize('send work orders');

    $result = app(TelegramService::class)->resendReady($order);

    if ($result) {
        $order->update(['ready_sent_at' => now()]);
        return back()->with('success', 'READY notification resent');
    }

    return back()->with('error', 'Failed to resend notification');
}
```

### Route

```php
Route::post('/admin/orders/{order}/resend-ready', [OrderController::class, 'resendReady'])
    ->name('admin.orders.resend-ready');
```

### Vue Component

```vue
<template>
    <div class="telegram-section" v-if="order.status === 'RESERVED'">
        <h4>Telegram Notifications</h4>

        <div v-if="order.ready_sent_at" class="status-info">
            <span class="badge success">READY sent</span>
            <span class="timestamp">{{ order.ready_sent_at }}</span>
        </div>

        <button
            @click="resendReady"
            :disabled="loading"
            class="btn-secondary"
        >
            {{ loading ? 'Sending...' : 'Resend to Therapists' }}
        </button>
    </div>
</template>

<script setup>
import { ref } from 'vue'
import { router } from '@inertiajs/vue3'

const props = defineProps({ order: Object })
const loading = ref(false)

const resendReady = () => {
    loading.value = true
    router.post(route('admin.orders.resend-ready', props.order.id), {}, {
        onFinish: () => loading.value = false,
    })
}
</script>
```

---

## Error Handling

```php
// TelegramService.php

private function sendMessage(string $token, string $chatId, string $text): bool
{
    try {
        $response = Http::timeout(10)
            ->retry(3, 100)
            ->post(config('telegram.api_url') . $token . '/sendMessage', [
                'chat_id' => $chatId,
                'text' => $text,
                'parse_mode' => 'Markdown',
            ]);

        if (!$response->successful()) {
            Log::error('Telegram API error', [
                'status' => $response->status(),
                'body' => $response->body(),
                'chat_id' => $chatId,
            ]);
            return false;
        }

        return true;

    } catch (\Illuminate\Http\Client\ConnectionException $e) {
        Log::warning('Telegram connection timeout', ['error' => $e->getMessage()]);
        return false;

    } catch (\Exception $e) {
        Log::error('Telegram unexpected error', ['error' => $e->getMessage()]);
        return false;
    }
}
```

**Important**: Telegram errors should NOT break business operations. Always catch exceptions and log them, but allow the main flow to continue.

---

## Testing

### Feature Test

```php
// tests/Feature/TelegramNotificationTest.php

class TelegramNotificationTest extends TestCase
{
    use RefreshDatabase;

    public function test_new_notification_sent_on_order_creation()
    {
        Http::fake(['*telegram*' => Http::response(['ok' => true])]);

        // Create order
        $response = $this->postJson('/api/booking/orders', [
            'slot_id' => Slot::factory()->free()->create()->id,
            'massage_type_id' => MassageType::factory()->create()->id,
            'phone' => '+998901234567',
            'privacy_consent' => true,
        ]);

        $response->assertStatus(201);

        Http::assertSent(function ($request) {
            return str_contains($request->url(), 'sendMessage') &&
                   str_contains($request['text'], 'NEW');
        });
    }

    public function test_ready_sent_only_when_conditions_met()
    {
        Http::fake(['*telegram*' => Http::response(['ok' => true])]);

        $order = Order::factory()->create([
            'status' => Order::STATUS_RESERVED,
            'payment_status' => Order::PAY_PAID,
        ]);

        OrderConfirmation::factory()->create([
            'order_id' => $order->id,
            'call_outcome' => 'CONFIRMED',
            'address' => 'Test Address',
        ]);

        $telegram = app(TelegramService::class);
        $result = $telegram->sendReady($order);

        $this->assertTrue($result);

        Http::assertSent(function ($request) {
            return str_contains($request['text'], 'READY');
        });
    }

    public function test_ready_not_sent_if_not_paid()
    {
        Http::fake();

        $order = Order::factory()->create([
            'status' => Order::STATUS_RESERVED,
            'payment_status' => Order::PAY_INVOICED, // Not paid!
        ]);

        $telegram = app(TelegramService::class);
        $result = $telegram->sendReady($order);

        $this->assertFalse($result);
        Http::assertNothingSent();
    }
}
```

---

## Checklist

- [ ] Config file with bot tokens and chat IDs
- [ ] TelegramService with sendNew, sendPaid, sendReady methods
- [ ] Proper Markdown formatting for messages
- [ ] Links to admin, master day view, public order
- [ ] READY conditions validation
- [ ] Duplicate READY prevention (ready_sent_at)
- [ ] Resend READY button in admin
- [ ] Error handling (don't break main flow)
- [ ] Retry logic for network issues
- [ ] Logging for debugging
- [ ] Feature tests with HTTP fake

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/saidabbos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
