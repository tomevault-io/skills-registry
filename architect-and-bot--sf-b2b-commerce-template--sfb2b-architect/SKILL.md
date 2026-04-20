---
name: sfb2b-architect
description: Technical architecture guidance for Salesforce B2B Commerce platform covering data models, integration patterns, security, performance optimization, and architectural decisions for the {{clientName}} storefront Use when this capability is needed.
metadata:
  author: architect-and-bot
---

# Salesforce B2B Commerce Architecture Guide

## Data Model Patterns

### Product2 Structure

**Simple Products**

- Standard Product2 record with basic attributes
- Single price point per product
- Use when: catalog items with fixed specifications
- Example: Standard items, basic consumable products

**Configurable Products**

- Parent Product2 with child variants (Product2 records linked via parent field)
- Variant selection attributes stored in Product Attributes (standard Salesforce feature)
- Each variant has unique SKU and pricing
- Use when: products with size, color, material options
- Decision: Use Product2 with Variants vs CPQ Configurators based on complexity

**CPQ Configurators**

- CPQ integration for complex, dynamic configurations
- Multi-step configuration flows
- Rules engine for attribute dependencies
- Dynamic pricing based on configuration selections
- Use when: equipment bundles, custom kits, bulk order optimization
- Avoid: simple size/color variants (use standard Product2 instead)

### Buyer Account Hierarchy

- **Primary Buyer Account**: Organization entity
- **Sub-Accounts**: Departments (e.g., Procurement, Operations)
- **Buyer Groups**: Permission sets tied to role within organization
  - Organization Admin
  - Department Manager
  - Purchase Requester
  - Read-Only Viewer
- Account hierarchy defines visibility in catalog and entitlements
- Use Account Teams for account management delegation

### Buyer Group Entitlements

- **Pricebook2 Assignment**: Link buyer groups to specific pricebooks for pricing control
- **Product Entitlement**: Use ProductVisibility and PermissionSet to control product access
- **SKU-level Control**: Via custom metadata or Apex sharing rules
- Multi-buyer group support: single buyer can belong to multiple groups with merged entitlements
- Caching critical: pre-compute buyer entitlements during login/session init

### Pricebook2 Structure ({{skuCount}} Entries)

**Scalability Considerations**

- Split large Pricebook2 across multiple records if approaching 50K limit
- Use Product Categories to logically group pricing tiers
- Implementation options:
  1. Single Pricebook2 with {{skuCount}} PricebookEntry2 records (manageable with proper indexing)
  2. Multiple Pricebooks by buyer segment + base standard pricebook
  3. Custom pricing object if more flexibility needed

**Price List Management**

- Maintain version history via CreatedDate and custom IsActive field
- Implement delta sync from source system (ERP) for changes only
- Standard Pricebook2: base pricing for all products
- Customer-specific Pricebooks: negotiated pricing per buyer group
- Volume pricing: use tiered pricebook entries or custom pricing logic in Apex

**Field Indexing**

- Index on Product2Id, Pricebook2Id, IsActive for performance
- Avoid inefficient filtering by Description or custom fields

### Custom Metadata for Feature Toggles and Shipping Tables

**Feature Toggles (Custom Metadata Type)**

```
Metadata Type: B2B_Commerce_Feature__mdt
Fields:
- Label (unique identifier)
- DeveloperName
- Enable_CPQ_Configurator__c (Boolean)
- Enable_Guest_Checkout__c (Boolean)
- Enable_Subscription_Products__c (Boolean)
- Feature_Description__c
- Rollout_Percentage__c (for gradual rollouts)
```

**Shipping Tables (Custom Metadata Type)**

```
Metadata Type: Shipping_Rate_Table__mdt
Fields:
- Label (unique identifier)
- DeveloperName
- Carrier__c (FedEx, UPS, USPS)
- Origin_Zip__c
- Destination_Zip_Range_Start__c
- Destination_Zip_Range_End__c
- Weight_Threshold_Lbs__c
- Base_Rate__c
- Per_Pound_Rate__c
- Delivery_Days__c
- IsActive__c
```

Benefits: Deployable via metadata, no data reload required, cached query results, version control friendly

---

## Integration Architecture

### Middleware Patterns

**Synchronous Patterns**

- Product catalog sync: scheduled batch (off-peak) pulling from ERP
- Order submission: real-time callback to ERP system
- Inventory check: sync callout during cart validation
- Latency requirement: < 2 seconds for inventory checks, < 5 seconds for order creation
- Timeout handling: fallback to cached inventory if service unavailable

**Asynchronous Patterns**

- Customer master sync: event-driven from ERP via Platform Events
- Pricebook updates: batch job polling for delta changes
- Order fulfillment status: outbound message to marketing automation
- Advantage: decoupled systems, reduced Salesforce transaction load

**Platform Events**

- Publish on order placement: `Order_Created__e` with metadata (order ID, buyer group, total)
- Subscribe: ERP integration, shipping system, analytics pipeline
- Retention: 24 hours (standard)
- Publish pattern: use Flow or Apex, batch if > 10K events
- Consumer: Platform Event Trigger in middleware or async Apex

### ERP Order Creation Flow

**Trigger Point**: B2B Commerce checkout completion

**Flow**

1. Cart validation completes in B2B Commerce
2. Order record created in Salesforce with WebstoreId
3. Order status: `Pending`
4. Platform Event published: `Order_Created__e`
5. Middleware async callout to ERP
6. ERP creates purchase order, sends back PO number
7. Update Salesforce Order with external reference
8. Order status: `Confirmed`

**Key Considerations**

- Handle ERP sync failures with retry logic (middleware exponential backoff)
- Store ERP PO number in custom field: `ERP_PO_ID__c`
- Track sync status: `ERP_Sync_Status__c` (Pending/Synced/Error)
- Reconciliation: nightly batch job matching orders by buyer account + order date
- Guest order handling: pre-create Account for anonymous purchases

### CPQ Session Management

**Session Lifecycle**

1. Customer selects configurable product on PDP
2. LWC creates CPQ session via REST API
3. Session ID stored in component state and sessionStorage
4. Multi-step configuration flows retrieve/update via session ID
5. Final config saved to CartItem as JSON in custom field
6. Session cleanup: automatic after 24 hours (default)

**Integration Points**

- Product selection -> CPQ session init
- Attribute selection -> CPQ rules engine evaluates dependencies
- Dynamic pricing -> CPQ calculates based on configuration
- Add to cart -> Serialize CPQ output to `Configuration_JSON__c` on CartItem

**Caching**

- Cache CPQ product definitions in sessionStorage (survives page nav)
- Cache attribute options to reduce API calls (invalidate on product change)
- Pre-load configuration for edit flows

**Error Handling**

- Session timeout: prompt user to reconfigure
- API failures: fallback to basic product add (notify user)
- Rule validation failures: highlight invalid attribute combinations

### Tax Provider Callout Pattern

**Trigger Point**: Cart calculation engine

**REST Callout**

```apex
POST /tax/freeapi/EstimateTax
Headers: Authorization: Bearer <token>
Body: {
  "line": [{
    "number": "1",
    "description": "Product SKU",
    "qty": 10,
    "amount": 500.00
  }],
  "addresses": {
    "shipFrom": { "latitude": 47.6, "longitude": -122.3 },
    "shipTo": { "latitude": 42.3, "longitude": -71.0 }
  }
}
```

**Response Processing**

- Extract tax amount and tax detail (line-level)
- Store in CartItem as `Tax_Amount__c`
- Update Cart totals
- Return to LWC for display

**Performance Optimization**

- Cache tax results by (origin zip, destination zip, product category) for 1 hour
- Batch multiple line items into single callout if > 10 items
- Async callout pattern: submit job, poll for completion (avoid timeout for large orders)
- Fallback: use pre-calculated tax tables if tax provider unavailable

**Compliance**

- Ensure handling of tax-exempt accounts (passed via account flag)
- Support for multi-state shipping
- Tax jurisdiction override capability for special cases

### Payment Gateway Abstraction

**Pattern: Adapter Design Pattern**

```apex
public interface PaymentGatewayAdapter {
  PaymentResult processPayment(PaymentRequest req);
  PaymentStatus queryStatus(String transactionId);
  PaymentRefund refundPayment(String transactionId, Decimal amount);
}

public class PrimaryGatewayAdapter implements PaymentGatewayAdapter {
  // Primary gateway implementation
}

public class SecondaryGatewayAdapter implements PaymentGatewayAdapter {
  // Secondary gateway implementation
}
```

**Decision Logic**

- Route by buyer group: select gateway based on account configuration
- Fallback chain: primary gateway down -> secondary gateway
- Test environment: use test keys, production -> live keys

**PCI Compliance**

- Never store card data in Salesforce
- Use tokenization: send card to gateway, receive token
- Store token reference only: `Payment_Token__c`
- All tokenization handled client-side (LWC -> gateway JS SDK)

**Integration Points**

- Checkout step: render payment form (gateway iFrame or hosted checkout)
- Order submission: call Apex method with token
- Apex: call adapter to process payment
- Response: payment status stored on Order

---

## B2B Commerce Specifics

### Experience Cloud LWR Architecture

**LWR (Lightning Web Runtime)**

- Modern, faster runtime vs Aura
- Component-based architecture
- Supports all standard B2B Commerce components
- Custom LWC components integrate seamlessly

**Site Structure**

```
Commerce Cloud Site
├── Home Page (LWR template)
├── Product Listing Page (PCP) - category-based
├── Product Detail Page (PDP)
├── Cart Page
├── Checkout Flow (multi-step)
├── Order History
└── Account Management
```

**Layout Pattern**

- Header: search, navigation, cart icon, account menu
- Left sidebar: category navigation or filters
- Main content: product grid or checkout step
- Right sidebar: optional (related products, promotions)
- Footer: company info, links, legal

### Commerce Webstore Setup

**Webstore Configuration**

- Name: `{{projectPrefix}}_B2B`
- Type: B2B
- Inventory sync: enable, sync every 1 hour
- Caching: enable product/pricing caching
- CDN: enable for static assets

**Catalog Association**

- Default Catalog: `{{projectPrefix}}_Products`
- Channels: B2B Webstore
- Category Hierarchy: align with buyer group entitlements

**Entitlements Configuration**

- Buyer Group linking
- Price Book assignment per buyer group
- Product visibility rules per category

### Catalog/Category/Entitlement Model

**Product Catalog**

- Root catalog: all {{skuCount}} products
- Sub-catalogs by department
- Category tree depth: 3 levels (Department -> Category -> Subcategory)

**Category Hierarchy Example**

```
Department A
├── Category 1
│   ├── Subcategory 1a
│   └── Subcategory 1b
├── Category 2
│   ├── Subcategory 2a
│   └── Subcategory 2b
└── Category 3
```

**Entitlements**

- ProductVisibility object links products to Buyer Groups
- Cascading: if buyer group has category access, show all products
- Override: can explicitly hide specific SKUs from buyer group
- Pricing: multiple Pricebook2 entries per product (volume tiers)

**Caching Strategy**

- Cache product -> category mappings (static until deployment)
- Cache entitlements per buyer group (refresh on buyer group change)
- Cache pricebook entries (delta sync on price change)
- TTL: 1 hour or event-driven refresh

### Cart Extension Points

**Cart Calculator**

- Custom Apex class extending `CartCalculator`
- Called post-add, pre-checkout
- Responsibilities: tax calculation, discount application, shipping estimate
- Access: cart items, buyer group, account
- Output: updated line items and totals

**Cart Validator**

- Custom Apex class implementing cart validation logic
- Called before checkout
- Checks: inventory availability, buyer group entitlements, stock allocation
- Return: error messages if validation fails
- Async pattern: submit validation job if > 5 second processing needed

**Checkout Flow Customization**

- Multi-step checkout: shipping -> billing -> payment -> review
- Custom step for buyer approval (if order > $X)
- Custom step for delivery location selection (multi-facility support)
- Each step is an LWC component with validation logic

---

## Security Model

### Permission Sets Per Persona (8 Types)

1. **Buyer (Standard)**
   - View products in assigned categories
   - View prices (assigned pricebook)
   - Create/edit own orders
   - View own order history
   - Limited account profile edit (name, email)

2. **Buyer Admin**
   - All Buyer permissions
   - Manage other buyer accounts (sub-users)
   - Approve orders > $X
   - View account analytics
   - Manage payment methods and addresses

3. **Purchase Approver**
   - View open orders in buyer group
   - Approve/reject orders > $X
   - Edit order shipping address before confirmation
   - Email notifications on order status changes

4. **Guest Shopper**
   - Browse catalog (public products only)
   - Add to cart
   - Checkout without login (create order as guest)
   - No account history access

5. **B2B Commerce Admin (Internal)**
   - Manage all webstore settings
   - Create/edit products and pricing
   - View all orders across buyers
   - Configure buyer groups and entitlements
   - System monitoring

6. **Integration Service User**
   - Read/write orders, accounts, products
   - Execute middleware flows
   - Publish Platform Events
   - No UI access (API only)

7. **Customer Success Manager**
   - View assigned buyer accounts and orders
   - Create orders on behalf of buyers
   - Edit buyer group assignments
   - Generate usage reports

8. **Analytics/Reporting User**
   - Read-only access to all B2B data
   - Create/edit custom reports and dashboards
   - Query via APIs for data export
   - No order modification

### Buyer Group Assignments

- Single buyer can belong to multiple groups
- Permissions merge (union of all group permissions)
- Default group for new buyers: `Standard Buyer`
- Admin can override group assignments
- Group assignments stored in `BuyerGroupMember` object

### Object-Level and Field-Level Security

**Object-Level Security**

- `Order` object: visible to assigned buyer group only
- `Account` object: visible to own account + parent accounts
- `Product2` object: filtered by ProductVisibility
- `CartItem` object: visible only to cart owner
- `Pricebook2` object: read-only, visibility via entitlements

**Field-Level Security**

- `Order.Total_Amount__c`: hidden from Guest Shoppers
- `Product2.Cost__c`: hidden from all buyer-facing users
- `Account.Credit_Limit__c`: hidden from buyers
- `CartItem.Discount_Applied__c`: hidden from non-admin users
- Implement via Profile/PermissionSet FLS rules

### Guest User Access Control

- Guest user profile: limited to public product browse + guest checkout
- Session timeout: 30 minutes inactivity (auto-logout)
- Restrictions:
  - No login required
  - Cannot view other users' orders
  - Cannot access account settings
  - Single-use shopping session
- Security: IP throttling on guest order creation (max 10 orders/hour per IP)
- Fraud prevention: email verification before order confirmation (send OTP)

---

## Performance Patterns

### Caching Strategy for Product Data

**3-Tier Caching Approach**

1. **Application Cache (Salesforce Cache)**
   - Cache product definitions (name, description, images) by product ID
   - TTL: 1 hour (or event-driven on product update)
   - Size limit: 5MB per partition
   - Use case: avoid repeated queries during session

2. **Browser Cache (sessionStorage/localStorage)**
   - Cache product details for current session
   - Cache category taxonomy
   - Cache user's pricebook entries
   - TTL: session duration or manual invalidation on logout
   - Size limit: 5-10MB per domain

3. **CDN Cache**
   - Static product images: cached for 24 hours
   - Product metadata JSON: cached for 1 hour (Cloudflare)
   - Setup: configure cache headers on media content

**Invalidation Strategy**

- Event-driven: publish Platform Event on product change -> invalidate app cache
- Time-based: TTL expiration (safeguard if events fail)
- Manual: admin trigger to flush all caches
- Partial: invalidate specific product ID rather than entire cache

### Pricebook Sync Optimization (Delta Sync)

**Problem**: {{skuCount}} products, nightly price updates create 24-hour lag

**Solution: Delta Sync Pattern**

1. **Source System (ERP)**
   - Maintain change log: `product_id, last_updated_timestamp`
   - Expose API: `GET /prices?since=<timestamp>` returns only changed prices

2. **Salesforce**
   - Scheduled job: runs hourly
   - Query timestamp of last sync: `LastSync__c`
   - Call ERP API with `since` filter
   - Receive delta: list of product IDs with new prices
   - Update only changed PricebookEntry2 records (bulk operation)
   - Update `LastSync__c` timestamp
   - Log sync results: success count, error count, failures

3. **Performance Impact**
   - Without delta: {{skuCount}} DML operations per sync
   - With delta: avg 500 DML operations per sync (assuming 1% change rate)
   - Reduces job duration: 10+ minutes -> 30 seconds
   - Reduces API calls: 1 bulk query instead of {{skuCount}} individual lookups

**Implementation**

```apex
List<String> changedProductIds = callERP('GET /prices?since=' + lastSyncTime);
List<PricebookEntry> entries = [SELECT Id, Product2Id, UnitPrice FROM PricebookEntry
                                WHERE Product2Id IN :changedProductIds];
// Update entries with new prices
update entries;
```

### Cart Performance with 200+ Line Items

**Challenges**

- Loading cart with 200+ items: slow query if not optimized
- Rendering 200 line items in LWC: browser memory pressure
- Cart totals calculation: Apex timeout risk

**Solutions**

1. **Virtual Scrolling (LWC)**
   - Render only visible items (e.g., 10-15 on screen)
   - Render items above/below viewport to buffer
   - Scroll listener: dynamically load/unload items
   - Benefit: smooth scrolling for 200+ items

2. **Pagination**
   - Display 50 items per page
   - User navigates via prev/next
   - Each page load: query 50 items only
   - Total items indicator: "1-50 of 247 items"

3. **Lazy Loading**
   - Load cart metadata (count, subtotal) immediately
   - Load full line items on tab click or expand
   - Benefit: cart page loads in <1 second

4. **Index Optimization**
   - Index on CartId + CreatedDate for efficient line item queries
   - Avoid SELECT \* -- query specific fields only

5. **Apex Cart Calculator**
   - Process in batches: 100 items per batch
   - Use Database.executeBatch() for large carts
   - Avoid N+1 queries: fetch all pricebook entries in single query

**Query Example**

```apex
List<CartItem> items = [SELECT Id, Product2Id, Quantity, SalesPrice
                         FROM CartItem
                         WHERE CartId = :cartId
                         ORDER BY CreatedDate DESC
                         LIMIT 100 OFFSET :offset];
```

### Search Index Configuration

**Search Setup**

- Index product catalog in Salesforce search
- Enable product synonyms (e.g., "item A" -> "item B")
- Custom fields to index: SKU, Category, Department
- Exclude fields: Cost, internal comments

**Search Experience Cloud Integration**

- Use standard search component with custom styling
- Faceted search: filter by category, price range, supplier
- Search results: show product image, price, availability
- Autocomplete: suggest products as user types (min 2 chars)

**Optimization**

- Index refresh frequency: real-time for product changes
- Search result limit: paginate at 20 results per page
- Relevance tuning: boost product title over description
- Analytics: track popular searches for merchandising insights

---

## Decision Framework

### When to Use Standard Components vs Custom LWC

**Use Standard B2B Commerce Components When**

- Component functionality matches requirement exactly
- No custom styling or behavior needed beyond SLDS theming
- Examples: ProductTile, ProductDetails (if no custom configs), Cart
- Benefit: supported by Salesforce, future-proof updates
- Maintenance: minimal custom code to maintain

**Use Custom LWC When**

- Standard component missing critical feature (e.g., CPQ integration on PDP)
- Custom styling required (brand compliance)
- Non-standard business logic (special pricing, bundling, approval flows)
- Integration with external systems (CPQ, tax provider)
- Examples: custom PDP for configurable products, custom checkout step for approval

**Decision Matrix**

```
Requirement Complexity    | Use Standard | Use Custom
Simple display of data    |     Y        |
+ Basic SLDS styling      |     Y        |
+ Custom business logic   |              |     Y
+ External integration    |              |     Y
+ Brand-specific UI       |              |     Y
```

### When to Use Apex vs Flow

**Use Apex When**

- Complex logic with loops, conditions, error handling
- High-volume processing (bulk operations on 1000+ records)
- Need for custom exception handling
- Integration with external APIs (callouts)
- Performance-critical operations (cart calculation, inventory check)
- Requirement: < 100ms execution time
- Testability: unit test coverage required

**Use Flow When**

- Simple, linear business logic
- Record create/update/delete operations
- Email notifications, scheduled actions
- Lead scoring, assignment rules
- Requirement: < 5 second execution time
- Non-technical admin should be able to modify logic
- No external API callouts needed

**Examples**

- Product entitlement check: Apex (complex decision tree)
- Send order confirmation email: Flow (simple notification)
- Pricebook sync from ERP: Apex (bulk API calls, complex data transform)
- Update order status when payment received: Flow (simple record update)

### When to Use Platform Events vs Synchronous Callouts

**Use Platform Events (Asynchronous) When**

- Decoupling required (ERP should not be down when order placed)
- High volume of events (order creation, inventory updates)
- Consumer is fire-and-forget (don't need immediate response)
- Scalability: handling 1000+ events/hour
- Examples: order placement, fulfillment status, inventory sync

**Use Synchronous Callouts When**

- Immediate response required (inventory check before add-to-cart)
- Validation needed before proceeding (tax calculation before checkout)
- Single consumer system (one-to-one integration)
- Low volume (< 100 callouts/hour)
- User is waiting for response (timeout acceptable: < 2 sec)
- Examples: cart validation, shipping rate lookup, tax calculation

**Hybrid Approach (Recommended)**

1. Synchronous: inventory check during add-to-cart (fail fast if out of stock)
2. Asynchronous: order creation trigger -> publish Platform Event for ERP sync
3. Sync callback: once ERP confirms, use webhook to update Salesforce order status

**Decision Diagram**

```
Is immediate response needed?
├─ YES -> Synchronous callout (< 2 sec requirement)
└─ NO -> Platform Event (async consumer)

High volume (>1000/hour)?
├─ YES -> Platform Event (avoid governor limits)
└─ NO -> Can use either (sync if simple, event if decoupling needed)
```

---

## Common Pitfalls & Solutions

**Data Sync Lag**: Product prices updated in ERP but not reflected in B2B Commerce for hours

- Solution: implement delta sync (see Pricebook Sync Optimization)

**Cart Timeouts**: Cart calculation with 200+ items fails

- Solution: batch processing, lazy loading, virtual scrolling

**Entitlement Explosion**: 1000 buyer groups x {{skuCount}} products = slow queries

- Solution: pre-compute entitlements, cache aggressively, use indexed queries

**Guest Checkout Abuse**: spam orders from single IP

- Solution: rate limiting, email verification, CAPTCHA on guest checkout

**Payment Token Exposure**: storing card data in custom fields

- Solution: use payment gateway tokenization, never store card data in Salesforce

---

## Related Decisions

See also:

- [sfb2b-lwc](./skills/sfb2b-lwc/SKILL.md) for component implementation guidance
- Performance testing strategy (load test with 200+ concurrent users)
- Disaster recovery plan for ERP sync failures
- Data retention policy for orders (per compliance requirements)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/architect-and-bot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
