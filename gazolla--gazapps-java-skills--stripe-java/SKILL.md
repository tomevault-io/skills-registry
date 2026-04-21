---
name: stripe-java
description: Stripe SDK for Java covering Payment Intents (one-time payments) and Subscriptions (recurring billing). Framework-agnostic patterns that work with any Java application. Use when this capability is needed.
metadata:
  author: gazolla
---

# Stripe Java SDK

Payment processing with Stripe: one-time payments and subscriptions.

## Use this skill when

- Implementing payment processing
- Setting up subscription/recurring billing
- Creating Stripe customers, products, prices
- User mentions "Stripe", "payments", "subscriptions", or "billing"

## Do not use this skill when

- User needs Spring Boot integration (use spring-stripe-integration)
- User wants other payment providers (PayPal, etc.)

## Dependencies

```xml
<dependency>
    <groupId>com.stripe</groupId>
    <artifactId>stripe-java</artifactId>
    <version>24.0.0</version>
</dependency>
```

## Configuration

```java
// Set API key at application startup
Stripe.apiKey = System.getenv("STRIPE_SECRET_KEY");

// Optional: Set API version
Stripe.setApiVersion("2023-10-16");
```

### Environment Variables

```properties
STRIPE_SECRET_KEY=sk_test_...
STRIPE_PUBLISHABLE_KEY=pk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...
```

## Core Concepts

### Stripe Object Hierarchy

```
Customer
    |-- PaymentMethod (card, bank account)
    |-- Subscription
    |       |-- SubscriptionItem
    |       |       |-- Price -> Product
    |-- Invoice
    |-- PaymentIntent (one-time payment)
```

## Customers

```java
public class StripeCustomerService {

    public Customer createCustomer(String email, String name) throws StripeException {
        CustomerCreateParams params = CustomerCreateParams.builder()
            .setEmail(email)
            .setName(name)
            .putMetadata("app_user_id", "internal-id-123")
            .build();
        
        return Customer.create(params);
    }

    public Customer getCustomer(String customerId) throws StripeException {
        return Customer.retrieve(customerId);
    }

    public Customer updateCustomer(String customerId, String newEmail) throws StripeException {
        Customer customer = Customer.retrieve(customerId);
        
        CustomerUpdateParams params = CustomerUpdateParams.builder()
            .setEmail(newEmail)
            .build();
        
        return customer.update(params);
    }

    public Customer deleteCustomer(String customerId) throws StripeException {
        Customer customer = Customer.retrieve(customerId);
        return customer.delete();
    }
}
```

## Products and Prices

```java
public class StripeProductService {

    // Create a product (e.g., "Pro Plan")
    public Product createProduct(String name, String description) throws StripeException {
        ProductCreateParams params = ProductCreateParams.builder()
            .setName(name)
            .setDescription(description)
            .build();
        
        return Product.create(params);
    }

    // Create a recurring price (subscription)
    public Price createRecurringPrice(
            String productId, 
            long amountCents, 
            String currency,
            String interval) throws StripeException {
        
        PriceCreateParams params = PriceCreateParams.builder()
            .setProduct(productId)
            .setUnitAmount(amountCents) // e.g., 1999 = $19.99
            .setCurrency(currency)      // e.g., "usd", "brl"
            .setRecurring(
                PriceCreateParams.Recurring.builder()
                    .setInterval(PriceCreateParams.Recurring.Interval.valueOf(interval.toUpperCase()))
                    .build()
            )
            .build();
        
        return Price.create(params);
    }

    // Create a one-time price
    public Price createOneTimePrice(
            String productId, 
            long amountCents, 
            String currency) throws StripeException {
        
        PriceCreateParams params = PriceCreateParams.builder()
            .setProduct(productId)
            .setUnitAmount(amountCents)
            .setCurrency(currency)
            .build();
        
        return Price.create(params);
    }
}
```

## Payment Intents (One-Time Payments)

```java
public class StripePaymentService {

    /**
     * Create a PaymentIntent for one-time payment.
     * Returns client_secret for frontend to confirm payment.
     */
    public PaymentIntent createPaymentIntent(
            String customerId,
            long amountCents,
            String currency,
            String description) throws StripeException {
        
        PaymentIntentCreateParams params = PaymentIntentCreateParams.builder()
            .setCustomer(customerId)
            .setAmount(amountCents)
            .setCurrency(currency)
            .setDescription(description)
            .setAutomaticPaymentMethods(
                PaymentIntentCreateParams.AutomaticPaymentMethods.builder()
                    .setEnabled(true)
                    .build()
            )
            .putMetadata("order_id", "order-123")
            .build();
        
        return PaymentIntent.create(params);
    }

    /**
     * Retrieve PaymentIntent to check status.
     */
    public PaymentIntent getPaymentIntent(String paymentIntentId) throws StripeException {
        return PaymentIntent.retrieve(paymentIntentId);
    }

    /**
     * Cancel a PaymentIntent.
     */
    public PaymentIntent cancelPaymentIntent(String paymentIntentId) throws StripeException {
        PaymentIntent intent = PaymentIntent.retrieve(paymentIntentId);
        return intent.cancel();
    }
}
```

### PaymentIntent Status Flow

```
requires_payment_method -> requires_confirmation -> requires_action -> processing -> succeeded
                                                                                  -> canceled
                                                                                  -> requires_payment_method (failed)
```

## Subscriptions

```java
public class StripeSubscriptionService {

    /**
     * Create a subscription for a customer.
     */
    public Subscription createSubscription(
            String customerId,
            String priceId) throws StripeException {
        
        SubscriptionCreateParams params = SubscriptionCreateParams.builder()
            .setCustomer(customerId)
            .addItem(
                SubscriptionCreateParams.Item.builder()
                    .setPrice(priceId)
                    .build()
            )
            .setPaymentBehavior(SubscriptionCreateParams.PaymentBehavior.DEFAULT_INCOMPLETE)
            .addExpand("latest_invoice.payment_intent")
            .build();
        
        return Subscription.create(params);
    }

    /**
     * Create subscription with trial period.
     */
    public Subscription createSubscriptionWithTrial(
            String customerId,
            String priceId,
            int trialDays) throws StripeException {
        
        SubscriptionCreateParams params = SubscriptionCreateParams.builder()
            .setCustomer(customerId)
            .addItem(
                SubscriptionCreateParams.Item.builder()
                    .setPrice(priceId)
                    .build()
            )
            .setTrialPeriodDays((long) trialDays)
            .build();
        
        return Subscription.create(params);
    }

    /**
     * Get subscription details.
     */
    public Subscription getSubscription(String subscriptionId) throws StripeException {
        return Subscription.retrieve(subscriptionId);
    }

    /**
     * Cancel subscription at period end (user keeps access until end of billing period).
     */
    public Subscription cancelAtPeriodEnd(String subscriptionId) throws StripeException {
        Subscription subscription = Subscription.retrieve(subscriptionId);
        
        SubscriptionUpdateParams params = SubscriptionUpdateParams.builder()
            .setCancelAtPeriodEnd(true)
            .build();
        
        return subscription.update(params);
    }

    /**
     * Cancel subscription immediately.
     */
    public Subscription cancelImmediately(String subscriptionId) throws StripeException {
        Subscription subscription = Subscription.retrieve(subscriptionId);
        return subscription.cancel();
    }

    /**
     * Change subscription plan (upgrade/downgrade).
     */
    public Subscription changePlan(
            String subscriptionId,
            String newPriceId) throws StripeException {
        
        Subscription subscription = Subscription.retrieve(subscriptionId);
        String itemId = subscription.getItems().getData().get(0).getId();
        
        SubscriptionUpdateParams params = SubscriptionUpdateParams.builder()
            .addItem(
                SubscriptionUpdateParams.Item.builder()
                    .setId(itemId)
                    .setPrice(newPriceId)
                    .build()
            )
            .setProrationBehavior(SubscriptionUpdateParams.ProrationBehavior.CREATE_PRORATIONS)
            .build();
        
        return subscription.update(params);
    }

    /**
     * Resume a canceled subscription (if still in period).
     */
    public Subscription resumeSubscription(String subscriptionId) throws StripeException {
        Subscription subscription = Subscription.retrieve(subscriptionId);
        
        SubscriptionUpdateParams params = SubscriptionUpdateParams.builder()
            .setCancelAtPeriodEnd(false)
            .build();
        
        return subscription.update(params);
    }
}
```

### Subscription Status Values

| Status | Description |
|--------|-------------|
| `incomplete` | Payment failed on creation |
| `incomplete_expired` | First payment never completed |
| `trialing` | In trial period |
| `active` | Paid and active |
| `past_due` | Payment failed, retrying |
| `canceled` | Canceled |
| `unpaid` | All retries failed |

## Payment Methods

```java
public class StripePaymentMethodService {

    /**
     * Attach a payment method to a customer.
     */
    public PaymentMethod attachToCustomer(
            String paymentMethodId,
            String customerId) throws StripeException {
        
        PaymentMethod paymentMethod = PaymentMethod.retrieve(paymentMethodId);
        
        PaymentMethodAttachParams params = PaymentMethodAttachParams.builder()
            .setCustomer(customerId)
            .build();
        
        return paymentMethod.attach(params);
    }

    /**
     * Set as default payment method for invoices.
     */
    public Customer setDefaultPaymentMethod(
            String customerId,
            String paymentMethodId) throws StripeException {
        
        Customer customer = Customer.retrieve(customerId);
        
        CustomerUpdateParams params = CustomerUpdateParams.builder()
            .setInvoiceSettings(
                CustomerUpdateParams.InvoiceSettings.builder()
                    .setDefaultPaymentMethod(paymentMethodId)
                    .build()
            )
            .build();
        
        return customer.update(params);
    }

    /**
     * List customer's payment methods.
     */
    public List<PaymentMethod> listPaymentMethods(String customerId) throws StripeException {
        PaymentMethodListParams params = PaymentMethodListParams.builder()
            .setCustomer(customerId)
            .setType(PaymentMethodListParams.Type.CARD)
            .build();
        
        return PaymentMethod.list(params).getData();
    }

    /**
     * Detach payment method from customer.
     */
    public PaymentMethod detach(String paymentMethodId) throws StripeException {
        PaymentMethod paymentMethod = PaymentMethod.retrieve(paymentMethodId);
        return paymentMethod.detach();
    }
}
```

## Error Handling

```java
public class StripeErrorHandler {

    public void handleStripeOperation(Runnable operation) {
        try {
            operation.run();
        } catch (CardException e) {
            // Card was declined
            String code = e.getCode(); // e.g., "card_declined", "insufficient_funds"
            String message = e.getMessage();
            log.error("Card error [{}]: {}", code, message);
            throw new PaymentDeclinedException(message, code);
            
        } catch (RateLimitException e) {
            // Too many requests
            log.warn("Stripe rate limit hit, retrying...");
            throw new PaymentServiceUnavailableException("Please try again");
            
        } catch (InvalidRequestException e) {
            // Invalid parameters
            log.error("Invalid Stripe request: {}", e.getMessage());
            throw new PaymentConfigurationException(e.getMessage());
            
        } catch (AuthenticationException e) {
            // API key problems
            log.error("Stripe authentication failed");
            throw new PaymentConfigurationException("Payment service configuration error");
            
        } catch (ApiConnectionException e) {
            // Network problems
            log.error("Could not connect to Stripe: {}", e.getMessage());
            throw new PaymentServiceUnavailableException("Payment service unavailable");
            
        } catch (StripeException e) {
            // Generic Stripe error
            log.error("Stripe error: {}", e.getMessage());
            throw new PaymentException("Payment processing failed");
        }
    }
}
```

## Testing

### Test Mode

Use test API keys (`sk_test_...`) and test card numbers:

| Card Number | Scenario |
|-------------|----------|
| 4242424242424242 | Successful payment |
| 4000000000000002 | Card declined |
| 4000000000009995 | Insufficient funds |
| 4000002500003155 | Requires 3D Secure |

### Test Clocks (for subscriptions)

```java
// Create test clock for time manipulation
TestClockCreateParams clockParams = TestClockCreateParams.builder()
    .setFrozenTime(Instant.now().getEpochSecond())
    .build();
TestClock testClock = TestClock.create(clockParams);

// Create customer attached to test clock
CustomerCreateParams customerParams = CustomerCreateParams.builder()
    .setEmail("test@example.com")
    .setTestClock(testClock.getId())
    .build();

// Advance time to test billing cycles
TestClockAdvanceParams advanceParams = TestClockAdvanceParams.builder()
    .setFrozenTime(Instant.now().plus(32, ChronoUnit.DAYS).getEpochSecond())
    .build();
testClock.advance(advanceParams);
```

## Code Quality Checklist

- [ ] API keys loaded from environment variables
- [ ] Error handling covers all Stripe exception types
- [ ] Metadata used for internal reference IDs
- [ ] Test mode used in development
- [ ] Idempotency keys used for retries
- [ ] Webhook signature verification implemented

## References

- See `references/stripe-objects.md` for object reference
- See `examples/` for complete code examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gazolla) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
