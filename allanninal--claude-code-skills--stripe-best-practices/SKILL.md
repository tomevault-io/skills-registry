---
name: stripe-best-practices
description: Best practices for building Stripe integrations - payments, subscriptions, webhooks, and checkout. Use when implementing payment features or upgrading Stripe SDK versions. Use when this capability is needed.
metadata:
  author: allanninal
---

# Stripe Integration Best Practices

## When to Use This Skill

- Implementing payment processing
- Setting up subscriptions and billing
- Configuring webhooks
- Building checkout flows
- Upgrading Stripe SDK or API versions
- Handling payment errors and edge cases

## Payment Intents (Recommended Flow)

### Server-Side Setup

```typescript
import Stripe from 'stripe';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, {
  apiVersion: '2024-12-18.acacia',
});

// Create Payment Intent
app.post('/create-payment-intent', async (req, res) => {
  const { amount, currency, customerId } = req.body;

  try {
    const paymentIntent = await stripe.paymentIntents.create({
      amount, // Amount in cents
      currency,
      customer: customerId,
      automatic_payment_methods: { enabled: true },
      metadata: {
        orderId: 'order_123',
      },
    });

    res.json({ clientSecret: paymentIntent.client_secret });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});
```

### Client-Side (React)

```typescript
import { loadStripe } from '@stripe/stripe-js';
import { Elements, PaymentElement, useStripe, useElements } from '@stripe/react-stripe-js';

const stripePromise = loadStripe(process.env.NEXT_PUBLIC_STRIPE_KEY!);

function CheckoutForm() {
  const stripe = useStripe();
  const elements = useElements();
  const [processing, setProcessing] = useState(false);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    if (!stripe || !elements) return;

    setProcessing(true);

    const { error } = await stripe.confirmPayment({
      elements,
      confirmParams: {
        return_url: `${window.location.origin}/payment-success`,
      },
    });

    if (error) {
      setError(error.message);
    }
    setProcessing(false);
  };

  return (
    <form onSubmit={handleSubmit}>
      <PaymentElement />
      <button disabled={!stripe || processing}>
        {processing ? 'Processing...' : 'Pay'}
      </button>
    </form>
  );
}

// Wrap with Elements provider
function Checkout({ clientSecret }: { clientSecret: string }) {
  return (
    <Elements stripe={stripePromise} options={{ clientSecret }}>
      <CheckoutForm />
    </Elements>
  );
}
```

## Webhooks (Critical)

### Webhook Handler

```typescript
import { buffer } from 'micro';

export const config = { api: { bodyParser: false } };

export default async function handler(req, res) {
  const sig = req.headers['stripe-signature'];
  const buf = await buffer(req);

  let event: Stripe.Event;

  try {
    event = stripe.webhooks.constructEvent(
      buf,
      sig,
      process.env.STRIPE_WEBHOOK_SECRET!
    );
  } catch (err) {
    console.error('Webhook signature verification failed');
    return res.status(400).send(`Webhook Error: ${err.message}`);
  }

  // Handle the event
  switch (event.type) {
    case 'payment_intent.succeeded':
      await handlePaymentSuccess(event.data.object);
      break;
    case 'payment_intent.payment_failed':
      await handlePaymentFailed(event.data.object);
      break;
    case 'customer.subscription.created':
      await handleSubscriptionCreated(event.data.object);
      break;
    case 'customer.subscription.updated':
      await handleSubscriptionUpdated(event.data.object);
      break;
    case 'customer.subscription.deleted':
      await handleSubscriptionCanceled(event.data.object);
      break;
    case 'invoice.payment_succeeded':
      await handleInvoicePaid(event.data.object);
      break;
    case 'invoice.payment_failed':
      await handleInvoicePaymentFailed(event.data.object);
      break;
    default:
      console.log(`Unhandled event type: ${event.type}`);
  }

  res.json({ received: true });
}
```

### Webhook Best Practices

- **Idempotency**: Always check if event was already processed
- **Respond quickly**: Return 200 within 30 seconds, process async
- **Verify signatures**: Never skip signature verification
- **Handle retries**: Stripe retries failed webhooks

```typescript
async function handlePaymentSuccess(paymentIntent: Stripe.PaymentIntent) {
  // Check idempotency
  const existing = await db.payment.findUnique({
    where: { stripePaymentIntentId: paymentIntent.id }
  });
  if (existing?.status === 'completed') return;

  // Process the payment
  await db.payment.upsert({
    where: { stripePaymentIntentId: paymentIntent.id },
    create: {
      stripePaymentIntentId: paymentIntent.id,
      amount: paymentIntent.amount,
      status: 'completed',
    },
    update: { status: 'completed' },
  });
}
```

## Subscriptions

### Create Subscription

```typescript
// Create customer
const customer = await stripe.customers.create({
  email: user.email,
  metadata: { userId: user.id },
});

// Create subscription
const subscription = await stripe.subscriptions.create({
  customer: customer.id,
  items: [{ price: 'price_xxx' }],
  payment_behavior: 'default_incomplete',
  payment_settings: {
    save_default_payment_method: 'on_subscription',
  },
  expand: ['latest_invoice.payment_intent'],
});

// Return client secret for payment
const clientSecret =
  (subscription.latest_invoice as Stripe.Invoice)
    .payment_intent?.client_secret;
```

### Customer Portal

```typescript
const session = await stripe.billingPortal.sessions.create({
  customer: customerId,
  return_url: `${process.env.APP_URL}/account`,
});

// Redirect to session.url
```

## Checkout Sessions (Quick Integration)

```typescript
const session = await stripe.checkout.sessions.create({
  mode: 'subscription', // or 'payment'
  line_items: [
    {
      price: 'price_xxx',
      quantity: 1,
    },
  ],
  success_url: `${process.env.APP_URL}/success?session_id={CHECKOUT_SESSION_ID}`,
  cancel_url: `${process.env.APP_URL}/canceled`,
  customer_email: user.email,
  metadata: {
    userId: user.id,
  },
});
```

## Error Handling

```typescript
try {
  await stripe.paymentIntents.create({ ... });
} catch (error) {
  if (error instanceof Stripe.errors.StripeCardError) {
    // Card was declined
    return { error: error.message, code: error.code };
  } else if (error instanceof Stripe.errors.StripeRateLimitError) {
    // Too many requests
    return { error: 'Too many requests, please retry' };
  } else if (error instanceof Stripe.errors.StripeInvalidRequestError) {
    // Invalid parameters
    console.error('Invalid request:', error.message);
  } else if (error instanceof Stripe.errors.StripeAPIError) {
    // Stripe API issue
    console.error('Stripe API error:', error.message);
  } else {
    console.error('Unknown error:', error);
  }
  throw error;
}
```

## Security Checklist

- [ ] Use HTTPS everywhere
- [ ] Store API keys in environment variables
- [ ] Verify webhook signatures
- [ ] Use restricted API keys in production
- [ ] Implement idempotency for webhooks
- [ ] Never log full card numbers
- [ ] Use Stripe.js for PCI compliance
- [ ] Enable Radar for fraud detection

## Testing

```bash
# Install CLI
brew install stripe/stripe-cli/stripe

# Login
stripe login

# Forward webhooks to local
stripe listen --forward-to localhost:3000/api/webhooks/stripe

# Trigger test events
stripe trigger payment_intent.succeeded
stripe trigger customer.subscription.created
```

## Version Upgrades

```typescript
// Always specify API version
const stripe = new Stripe(key, {
  apiVersion: '2024-12-18.acacia',
});

// Check changelog before upgrading:
// https://stripe.com/docs/upgrades
```

| API Version | Key Changes |
|-------------|-------------|
| 2024-12-18 | Payment Intents default, Checkout improvements |
| 2023-10-16 | New Invoice status values |
| 2023-08-16 | Subscription schedules update |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
