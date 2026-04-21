---
name: spring-stripe-integration
description: Stripe integration with Spring Boot including webhook handling, checkout sessions, customer portal, and subscription management with JPA persistence. Use when this capability is needed.
metadata:
  author: gazolla
---

# Spring Boot + Stripe Integration

Complete Stripe integration for Spring Boot applications.

## Use this skill when

- Integrating Stripe with Spring Boot
- Setting up webhook endpoints
- Using Stripe Checkout or Customer Portal
- Persisting subscription status in database
- User mentions "Stripe webhooks", "checkout session", or "customer portal"

## Configuration

### application.yml

```yaml
stripe:
  api-key: ${STRIPE_SECRET_KEY}
  publishable-key: ${STRIPE_PUBLISHABLE_KEY}
  webhook-secret: ${STRIPE_WEBHOOK_SECRET}
  
  prices:
    basic-monthly: price_xxxxx
    basic-yearly: price_xxxxx
    pro-monthly: price_xxxxx
    pro-yearly: price_xxxxx

app:
  base-url: ${APP_BASE_URL:http://localhost:8080}
```

### StripeConfig.java

```java
@Configuration
@ConfigurationProperties(prefix = "stripe")
@Getter
@Setter
public class StripeConfig {

    private String apiKey;
    private String publishableKey;
    private String webhookSecret;
    private Map<String, String> prices = new HashMap<>();

    @PostConstruct
    public void init() {
        Stripe.apiKey = apiKey;
    }
}
```

## User Entity with Stripe

```java
@Entity
@Table(name = "users")
@Getter
@Setter
public class User extends BaseEntity {

    private String email;
    private String name;
    
    @Column(name = "stripe_customer_id", unique = true)
    private String stripeCustomerId;

    @Enumerated(EnumType.STRING)
    @Column(name = "subscription_status")
    private SubscriptionStatus subscriptionStatus = SubscriptionStatus.NONE;

    @Column(name = "subscription_id")
    private String subscriptionId;

    @Column(name = "price_id")
    private String priceId;

    @Column(name = "current_period_end")
    private LocalDateTime currentPeriodEnd;

    public enum SubscriptionStatus {
        NONE, TRIALING, ACTIVE, PAST_DUE, CANCELED, UNPAID
    }

    public boolean hasActiveSubscription() {
        return subscriptionStatus == SubscriptionStatus.ACTIVE 
            || subscriptionStatus == SubscriptionStatus.TRIALING;
    }
}
```

## Stripe Service

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class StripeService {

    private final StripeConfig config;
    private final UserRepository userRepository;

    @Value("${app.base-url}")
    private String baseUrl;

    /**
     * Get or create Stripe customer for user.
     */
    @Transactional
    public String getOrCreateCustomer(User user) throws StripeException {
        if (user.getStripeCustomerId() != null) {
            return user.getStripeCustomerId();
        }

        CustomerCreateParams params = CustomerCreateParams.builder()
            .setEmail(user.getEmail())
            .setName(user.getName())
            .putMetadata("user_id", user.getId().toString())
            .build();

        Customer customer = Customer.create(params);
        user.setStripeCustomerId(customer.getId());
        userRepository.save(user);

        return customer.getId();
    }

    /**
     * Create Checkout Session for subscription.
     */
    public String createCheckoutSession(User user, String priceId) throws StripeException {
        String customerId = getOrCreateCustomer(user);

        SessionCreateParams params = SessionCreateParams.builder()
            .setCustomer(customerId)
            .setMode(SessionCreateParams.Mode.SUBSCRIPTION)
            .addLineItem(
                SessionCreateParams.LineItem.builder()
                    .setPrice(priceId)
                    .setQuantity(1L)
                    .build()
            )
            .setSuccessUrl(baseUrl + "/subscription/success?session_id={CHECKOUT_SESSION_ID}")
            .setCancelUrl(baseUrl + "/subscription/cancel")
            .putMetadata("user_id", user.getId().toString())
            .build();

        Session session = Session.create(params);
        return session.getUrl();
    }

    /**
     * Create Checkout Session for one-time payment.
     */
    public String createPaymentCheckout(User user, String priceId) throws StripeException {
        String customerId = getOrCreateCustomer(user);

        SessionCreateParams params = SessionCreateParams.builder()
            .setCustomer(customerId)
            .setMode(SessionCreateParams.Mode.PAYMENT)
            .addLineItem(
                SessionCreateParams.LineItem.builder()
                    .setPrice(priceId)
                    .setQuantity(1L)
                    .build()
            )
            .setSuccessUrl(baseUrl + "/payment/success?session_id={CHECKOUT_SESSION_ID}")
            .setCancelUrl(baseUrl + "/payment/cancel")
            .build();

        Session session = Session.create(params);
        return session.getUrl();
    }

    /**
     * Create Customer Portal session.
     */
    public String createPortalSession(User user) throws StripeException {
        if (user.getStripeCustomerId() == null) {
            throw new IllegalStateException("User has no Stripe customer");
        }

        com.stripe.param.billingportal.SessionCreateParams params =
            com.stripe.param.billingportal.SessionCreateParams.builder()
                .setCustomer(user.getStripeCustomerId())
                .setReturnUrl(baseUrl + "/account")
                .build();

        com.stripe.model.billingportal.Session session =
            com.stripe.model.billingportal.Session.create(params);

        return session.getUrl();
    }

    /**
     * Cancel subscription at period end.
     */
    public void cancelSubscription(User user) throws StripeException {
        if (user.getSubscriptionId() == null) {
            throw new IllegalStateException("User has no subscription");
        }

        Subscription subscription = Subscription.retrieve(user.getSubscriptionId());
        
        SubscriptionUpdateParams params = SubscriptionUpdateParams.builder()
            .setCancelAtPeriodEnd(true)
            .build();

        subscription.update(params);
    }
}
```

## Webhook Controller

```java
@RestController
@RequestMapping("/api/webhooks")
@RequiredArgsConstructor
@Slf4j
public class StripeWebhookController {

    private final StripeConfig config;
    private final StripeWebhookHandler webhookHandler;

    @PostMapping("/stripe")
    public ResponseEntity<String> handleWebhook(
            @RequestBody String payload,
            @RequestHeader("Stripe-Signature") String sigHeader) {

        Event event;
        try {
            event = Webhook.constructEvent(payload, sigHeader, config.getWebhookSecret());
        } catch (SignatureVerificationException e) {
            log.warn("Invalid Stripe signature");
            return ResponseEntity.badRequest().body("Invalid signature");
        }

        log.info("Received Stripe webhook: {}", event.getType());

        try {
            webhookHandler.handleEvent(event);
            return ResponseEntity.ok("OK");
        } catch (Exception e) {
            log.error("Error handling webhook {}: {}", event.getType(), e.getMessage());
            return ResponseEntity.status(500).body("Webhook handler error");
        }
    }
}
```

## Webhook Handler

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class StripeWebhookHandler {

    private final UserRepository userRepository;

    @Transactional
    public void handleEvent(Event event) {
        switch (event.getType()) {
            case "checkout.session.completed" -> handleCheckoutComplete(event);
            case "customer.subscription.created" -> handleSubscriptionCreated(event);
            case "customer.subscription.updated" -> handleSubscriptionUpdated(event);
            case "customer.subscription.deleted" -> handleSubscriptionDeleted(event);
            case "invoice.payment_succeeded" -> handlePaymentSucceeded(event);
            case "invoice.payment_failed" -> handlePaymentFailed(event);
            default -> log.debug("Unhandled event type: {}", event.getType());
        }
    }

    private void handleCheckoutComplete(Event event) {
        Session session = (Session) event.getDataObjectDeserializer()
            .getObject().orElseThrow();

        if ("subscription".equals(session.getMode())) {
            log.info("Subscription checkout completed for customer: {}", 
                session.getCustomer());
        }
    }

    private void handleSubscriptionCreated(Event event) {
        Subscription subscription = (Subscription) event.getDataObjectDeserializer()
            .getObject().orElseThrow();

        updateUserSubscription(subscription);
        log.info("Subscription created: {}", subscription.getId());
    }

    private void handleSubscriptionUpdated(Event event) {
        Subscription subscription = (Subscription) event.getDataObjectDeserializer()
            .getObject().orElseThrow();

        updateUserSubscription(subscription);
        log.info("Subscription updated: {} -> {}", 
            subscription.getId(), subscription.getStatus());
    }

    private void handleSubscriptionDeleted(Event event) {
        Subscription subscription = (Subscription) event.getDataObjectDeserializer()
            .getObject().orElseThrow();

        userRepository.findByStripeCustomerId(subscription.getCustomer())
            .ifPresent(user -> {
                user.setSubscriptionStatus(User.SubscriptionStatus.CANCELED);
                user.setSubscriptionId(null);
                userRepository.save(user);
            });

        log.info("Subscription deleted: {}", subscription.getId());
    }

    private void handlePaymentSucceeded(Event event) {
        Invoice invoice = (Invoice) event.getDataObjectDeserializer()
            .getObject().orElseThrow();

        log.info("Payment succeeded for invoice: {}", invoice.getId());
    }

    private void handlePaymentFailed(Event event) {
        Invoice invoice = (Invoice) event.getDataObjectDeserializer()
            .getObject().orElseThrow();

        userRepository.findByStripeCustomerId(invoice.getCustomer())
            .ifPresent(user -> {
                user.setSubscriptionStatus(User.SubscriptionStatus.PAST_DUE);
                userRepository.save(user);
            });

        log.warn("Payment failed for customer: {}", invoice.getCustomer());
        // TODO: Send email notification
    }

    private void updateUserSubscription(Subscription subscription) {
        userRepository.findByStripeCustomerId(subscription.getCustomer())
            .ifPresent(user -> {
                user.setSubscriptionId(subscription.getId());
                user.setSubscriptionStatus(mapStatus(subscription.getStatus()));
                user.setPriceId(subscription.getItems().getData().get(0).getPrice().getId());
                user.setCurrentPeriodEnd(
                    LocalDateTime.ofInstant(
                        Instant.ofEpochSecond(subscription.getCurrentPeriodEnd()),
                        ZoneOffset.UTC
                    )
                );
                userRepository.save(user);
            });
    }

    private User.SubscriptionStatus mapStatus(String stripeStatus) {
        return switch (stripeStatus) {
            case "trialing" -> User.SubscriptionStatus.TRIALING;
            case "active" -> User.SubscriptionStatus.ACTIVE;
            case "past_due" -> User.SubscriptionStatus.PAST_DUE;
            case "canceled" -> User.SubscriptionStatus.CANCELED;
            case "unpaid" -> User.SubscriptionStatus.UNPAID;
            default -> User.SubscriptionStatus.NONE;
        };
    }
}
```

## Subscription Controller

```java
@Controller
@RequestMapping("/subscription")
@RequiredArgsConstructor
public class SubscriptionController {

    private final StripeService stripeService;
    private final StripeConfig config;

    @GetMapping("/plans")
    public String plans(Model model) {
        model.addAttribute("prices", config.getPrices());
        model.addAttribute("publishableKey", config.getPublishableKey());
        return "subscription/plans";
    }

    @PostMapping("/checkout")
    public String checkout(
            @AuthenticationPrincipal CustomUserPrincipal principal,
            @RequestParam String priceId) throws StripeException {
        
        String checkoutUrl = stripeService.createCheckoutSession(
            principal.getUser(), priceId
        );
        return "redirect:" + checkoutUrl;
    }

    @GetMapping("/success")
    public String success(@RequestParam("session_id") String sessionId, Model model) {
        model.addAttribute("sessionId", sessionId);
        return "subscription/success";
    }

    @GetMapping("/cancel")
    public String cancel() {
        return "subscription/cancel";
    }

    @PostMapping("/portal")
    public String portal(@AuthenticationPrincipal CustomUserPrincipal principal) 
            throws StripeException {
        String portalUrl = stripeService.createPortalSession(principal.getUser());
        return "redirect:" + portalUrl;
    }

    @PostMapping("/cancel")
    public String cancelSubscription(
            @AuthenticationPrincipal CustomUserPrincipal principal,
            RedirectAttributes redirectAttributes) throws StripeException {
        
        stripeService.cancelSubscription(principal.getUser());
        redirectAttributes.addFlashAttribute("message", 
            "Subscription will be canceled at the end of the billing period");
        return "redirect:/account";
    }
}
```

## Subscription Guard (Access Control)

```java
@Component
@RequiredArgsConstructor
public class SubscriptionGuard {

    public boolean hasActiveSubscription(CustomUserPrincipal principal) {
        return principal != null && principal.getUser().hasActiveSubscription();
    }
}

// Usage in Controller
@GetMapping("/premium-feature")
@PreAuthorize("@subscriptionGuard.hasActiveSubscription(#principal)")
public String premiumFeature(@AuthenticationPrincipal CustomUserPrincipal principal) {
    return "premium/feature";
}

// Or in Thymeleaf
<div th:if="${#authentication.principal.user.hasActiveSubscription()}">
    Premium content here
</div>
```

## Webhook Testing (Local Development)

```bash
# Install Stripe CLI
# https://stripe.com/docs/stripe-cli

# Login to Stripe
stripe login

# Forward webhooks to local server
stripe listen --forward-to localhost:8080/api/webhooks/stripe

# Trigger test events
stripe trigger customer.subscription.created
stripe trigger invoice.payment_succeeded
```

## Code Quality Checklist

- [ ] Webhook signature verification implemented
- [ ] All webhook events handled with proper error handling
- [ ] User entity has Stripe fields
- [ ] Subscription status synced with database
- [ ] Customer Portal for self-service
- [ ] Environment variables for all keys
- [ ] Idempotent webhook processing

## References

- See `references/webhook-events.md` for event reference
- See `examples/` for complete code examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gazolla) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
