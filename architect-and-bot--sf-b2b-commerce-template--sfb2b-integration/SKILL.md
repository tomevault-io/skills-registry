---
name: sfb2b-integration
description: Salesforce B2B Commerce integration patterns for ERP, middleware, CPQ, tax provider, and payment gateway. Triggers on: integration, ERP, middleware, CPQ, tax, API, callout, sync, order sync, pricebook sync, tax calculation, payment Use when this capability is needed.
metadata:
  author: architect-and-bot
---

# Salesforce B2B Commerce Integration Patterns

## ERP Integration

The ERP system is the backend for {{clientName}}. All orders, inventory, and product data flow through the ERP via middleware.

### Cart -> ERP Sales Order Creation

**Flow**: SF B2B Cart -> Middleware -> ERP Sales Order

**Trigger Point**: Checkout completion (CheckoutSessionAction)

**Payload Structure**:

```json
{
  "customerId": "ERP-ACC-12345",
  "orderDate": "2024-02-07",
  "items": [
    {
      "lineNumber": 1,
      "productNumber": "ERP-PROD-789",
      "quantity": 5,
      "unitPrice": 299.99,
      "discountPercent": 10
    }
  ],
  "shippingAddress": {
    "street": "123 Main Street",
    "city": "Boston",
    "state": "MA",
    "zip": "02101"
  },
  "taxAmount": 234.56,
  "shippingAmount": 50.0,
  "totalAmount": 2484.56,
  "notes": "PO: ORD-2024-001"
}
```

**Apex Integration**:

```apex
public class {{projectPrefix}}_ERPOrderService {
    public ERPOrderDTO createSalesOrder(Order sfOrder) {
        {{projectPrefix}}_OrderSelector selector = new {{projectPrefix}}_OrderSelector();
        Order cart = selector.selectById(new Set<Id>{sfOrder.Id})[0];

        // Build ERP payload
        String payload = buildOrderPayload(cart);

        // Call Middleware API
        {{projectPrefix}}_ERPIntegration integration = new {{projectPrefix}}_ERPIntegration();
        ERPOrderDTO result = integration.createSalesOrder(payload);

        // Store ERP order number in SF
        cart.ERP_Order_Number__c = result.orderNumber;
        cart.External_System_Id__c = result.orderId;
        update cart;

        return result;
    }
}
```

**Error Handling**:

- Retry 3x with exponential backoff
- Publish `{{projectPrefix}}_OrderSyncFailedEvent__e` on failure
- Store failed payload in `Order_Integration_Log__c` for manual retry

### Pricebook Sync: {{skuCount}} Entries

**Volume**: {{skuCount}} pricebook entries from ERP Product Master

**Sync Strategy**: Delta sync nightly

- Only sync products with `LastModifiedDate` in last 24 hours
- Batch processing: 200 entries per batch
- Scheduled: 2:00 AM CST (low-traffic window)

**Data Mapping**:

```
ERP Field -> Salesforce Field
product_number -> Product2.External_Product_Id__c
description -> Product2.Description
unitPrice -> PricebookEntry.UnitPrice
listPrice -> PricebookEntry.List_Price__c
lastModified -> Product2.LastModifiedDate (from sync)
```

**Batch Apex Implementation**:

```apex
public class {{projectPrefix}}_PricebookSyncBatch implements Database.Batchable<SObject>, Database.Stateful {
    public Integer totalProcessed = 0;
    public Integer totalUpserted = 0;
    public Integer totalErrors = 0;

    public Database.QueryLocator start(Database.BatchableContext bc) {
        // Delta: only sync products modified in last 24 hours
        DateTime lastSync = {{projectPrefix}}_SyncStateService.getLastSyncTime('pricebook');
        return Database.getQueryLocator([
            SELECT Id, External_Product_Id__c, Name, UnitPrice
            FROM PricebookEntry
            WHERE LastModifiedDate >= :lastSync
            LIMIT 50000
        ]);
    }

    public void execute(Database.BatchableContext bc, List<PricebookEntry> scope) {
        // Process in chunks of 200
        Map<String, Product2> productsByExtId = {{projectPrefix}}_ProductSelector.selectByExternalIds(
            extractExternalIds(scope)
        );

        List<PricebookEntry> entriesToUpdate = new List<PricebookEntry>();
        for (PricebookEntry entry : scope) {
            Decimal newPrice = callERPPricingEngine(entry);
            entry.UnitPrice = newPrice;
            entriesToUpdate.add(entry);
        }

        Database.SaveResult[] results = Database.update(entriesToUpdate, false);
        for (Database.SaveResult result : results) {
            if (result.isSuccess()) {
                totalUpserted++;
            } else {
                totalErrors++;
                {{projectPrefix}}_Logger.error('PricebookSync', result.getErrors()[0].getMessage(), null);
            }
        }
        totalProcessed += scope.size();
    }

    public void finish(Database.BatchableContext bc) {
        // Update sync timestamp
        {{projectPrefix}}_SyncStateService.updateLastSyncTime('pricebook', DateTime.now());

        // Publish sync complete event
        {{projectPrefix}}_PricebookSyncCompleteEvent__e event = new {{projectPrefix}}_PricebookSyncCompleteEvent__e(
            Total_Processed__c = totalProcessed,
            Total_Upserted__c = totalUpserted,
            Total_Errors__c = totalErrors
        );
        EventBus.publish(event);
    }
}
```

**Scheduled Execution**:

```apex
public class {{projectPrefix}}_PricebookSyncSchedulable implements Schedulable {
    public void execute(SchedulableContext sc) {
        Database.executeBatch(new {{projectPrefix}}_PricebookSyncBatch(), 200);
    }
    // Schedule: CRON job at 02:00 CST daily
    // System.schedule('Pricebook Sync', '0 0 2 * * ?', new {{projectPrefix}}_PricebookSyncSchedulable());
}
```

### Order Status Sync: ERP -> Salesforce B2B

**SLA**: < 30 seconds from status change in ERP to update in Salesforce

**Sync Mechanism**: Platform Event subscriber listening to middleware events

**Status Values**:

```
ERP Status -> SF Order Status
"pending"        -> "Pending"
"confirmed"      -> "Processing"
"shipped"        -> "Shipped"
"delivered"      -> "Fulfilled"
"cancelled"      -> "Cancelled"
```

**Event Handler**:

```apex
public class {{projectPrefix}}_ERPStatusSyncHandler {
    @AuraEnabled(cacheable=false)
    public static void handleStatusChange(String erpOrderId, String newStatus) {
        // Find SF Order by External_System_Id
        Order sfOrder = [
            SELECT Id, Status, ERP_Order_Number__c
            FROM Order
            WHERE External_System_Id__c = :erpOrderId
            LIMIT 1
        ];

        // Map status and update
        String mappedStatus = mapERPStatus(newStatus);
        sfOrder.Status = mappedStatus;
        sfOrder.Last_Status_Sync__c = DateTime.now();

        update sfOrder;

        // Publish event for notifications
        publishOrderStatusChangeNotification(sfOrder, newStatus);
    }

    private static void publishOrderStatusChangeNotification(Order order, String status) {
        {{projectPrefix}}_OrderStatusChangeEvent__e event = new {{projectPrefix}}_OrderStatusChangeEvent__e(
            Order_Id__c = order.Id,
            New_Status__c = status,
            Changed_At__c = DateTime.now()
        );
        EventBus.publish(event);
    }
}
```

### Product Data Sync: ERP Product Master -> Product2

**Flow**: ERP Products -> Middleware -> Salesforce Product2

**Data Mapping**:

```
ERP Field          -> Salesforce Field
product_id              -> Product2.External_Product_Id__c
product_name            -> Product2.Name
description             -> Product2.Description
category_code           -> Product2.Product_Category__c
active_status           -> Product2.IsActive
unit_of_measure         -> Product2.Unit_Of_Measure__c
last_modified_timestamp -> Product2.External_LastModified__c
```

**Upsert Strategy**:

```apex
public class {{projectPrefix}}_ERPProductSyncService {
    public void syncProducts(List<ERPProductDTO> erpProducts) {
        // Build map of existing products
        Map<String, Product2> productsByExtId =
            {{projectPrefix}}_ProductSelector.selectByExternalIds(extractExternalIds(erpProducts));

        List<Product2> productsToUpsert = new List<Product2>();
        for (ERPProductDTO erpProduct : erpProducts) {
            Product2 sfProduct = productsByExtId.get(erpProduct.productId);
            if (sfProduct == null) {
                // Create new product
                sfProduct = new Product2();
            }

            // Update fields
            sfProduct.External_Product_Id__c = erpProduct.productId;
            sfProduct.Name = erpProduct.name;
            sfProduct.Description = erpProduct.description;
            sfProduct.Product_Category__c = erpProduct.categoryCode;
            sfProduct.IsActive = erpProduct.isActive;
            sfProduct.Unit_Of_Measure__c = erpProduct.uom;
            sfProduct.External_LastModified__c = erpProduct.lastModified;

            productsToUpsert.add(sfProduct);
        }

        // Upsert all products
        Database.UpsertResult[] results = Database.upsert(productsToUpsert, Product2.External_Product_Id__c, false);
        handleUpsertResults(results);
    }
}
```

### Invoice PDF Retrieval

**Flow**: Order -> Request PDF from ERP -> Store as Attachment

```apex
public class {{projectPrefix}}_InvoicePDFService {
    public Blob getInvoicePDF(String erpOrderNumber) {
        {{projectPrefix}}_ERPIntegration integration = new {{projectPrefix}}_ERPIntegration();
        HttpResponse response = integration.getInvoicePDF(erpOrderNumber);

        if (response.getStatusCode() == 200) {
            return response.getBodyAsBlob();
        } else {
            throw new {{projectPrefix}}_IntegrationException('Failed to retrieve invoice PDF');
        }
    }

    public void attachInvoiceToOrder(Id orderId, String erpOrderNumber) {
        Blob pdfBlob = getInvoicePDF(erpOrderNumber);

        Attachment att = new Attachment(
            ParentId = orderId,
            Name = 'Invoice_' + erpOrderNumber + '.pdf',
            Body = pdfBlob,
            ContentType = 'application/pdf'
        );
        insert att;
    }
}
```

## CPQ Integration

CPQ provides configuration logic for complex B2B products (equipment bundles, customized kits).

### Blueprint Session Management

**CPQ Blueprints** are configuration templates. Each blueprint session manages a product configuration.

**Create Session**:

```apex
public class {{projectPrefix}}_CPQService {
    private String cpqApiKey = 'callout:CPQ_API';

    public CPQSessionDTO createBlueprint(String blueprintId, String cartItemId) {
        String endpoint = cpqApiKey + '/api/blueprints/' + blueprintId + '/session';
        String payload = JSON.serialize(new Map<String, Object>{
            'externalId' => cartItemId,
            'context' => new Map<String, Object>{
                'accountId' => UserInfo.getOrganizationId(),
                'quantity' => 1
            }
        });

        HttpResponse response = callCPQAPI('POST', endpoint, payload);
        CPQSessionDTO session = (CPQSessionDTO)JSON.deserialize(response.getBody(), CPQSessionDTO.class);
        return session;
    }

    public void updateBlueprint(String sessionId, Map<String, Object> attributes) {
        String endpoint = cpqApiKey + '/api/sessions/' + sessionId;
        String payload = JSON.serialize(new Map<String, Object>{
            'attributes' => attributes
        });

        callCPQAPI('PUT', endpoint, payload);
    }

    public CPQSessionDTO finalizeBlueprint(String sessionId) {
        String endpoint = cpqApiKey + '/api/sessions/' + sessionId + '/finalize';
        HttpResponse response = callCPQAPI('POST', endpoint, '');
        return (CPQSessionDTO)JSON.deserialize(response.getBody(), CPQSessionDTO.class);
    }
}
```

### Attribute Entitlement Querying

Validate which configuration options are available for the current user/account:

```apex
public class {{projectPrefix}}_CPQEntitlementService {
    public List<CPQAttributeDTO> getAvailableAttributes(String blueprintId, Id accountId) {
        // Query CPQ for entitlements
        {{projectPrefix}}_CPQService cpqService = new {{projectPrefix}}_CPQService();
        List<CPQAttributeDTO> allAttributes = cpqService.getBlueprint(blueprintId);

        // Filter by entitlements
        {{projectPrefix}}_EntitlementSelector selector = new {{projectPrefix}}_EntitlementSelector();
        List<Entitlement__c> accountEntitlements = selector.selectByAccountId(accountId);

        // Build allowed values map
        Map<String, Set<String>> allowedValues = new Map<String, Set<String>>();
        for (Entitlement__c ent : accountEntitlements) {
            if (!allowedValues.containsKey(ent.Attribute__c)) {
                allowedValues.put(ent.Attribute__c, new Set<String>());
            }
            allowedValues.get(ent.Attribute__c).add(ent.Allowed_Value__c);
        }

        // Filter attributes
        List<CPQAttributeDTO> filtered = new List<CPQAttributeDTO>();
        for (CPQAttributeDTO attr : allAttributes) {
            if (allowedValues.containsKey(attr.name)) {
                attr.allowedValues = new List<String>(allowedValues.get(attr.name));
            }
            filtered.add(attr);
        }

        return filtered;
    }
}
```

### Fabric/Swatch Data Table Management

Configuration options with visual representations (color swatches, dimensions):

```apex
public class {{projectPrefix}}_CPQFabricService {
    public List<FabricOptionDTO> getFabricOptions(String blueprintId) {
        // Query CPQ Fabric library
        {{projectPrefix}}_CPQService cpqService = new {{projectPrefix}}_CPQService();
        List<FabricOptionDTO> fabrics = cpqService.getFabrics(blueprintId);

        // Attach images from content library
        for (FabricOptionDTO fabric : fabrics) {
            ContentVersion cv = [
                SELECT Id, VersionData
                FROM ContentVersion
                WHERE Title = :fabric.name AND IsLatest = true
                LIMIT 1
            ];
            fabric.swatchUrl = '/sfc/servlet.shepherd/version/download/' + cv.Id;
        }

        return fabrics;
    }
}
```

### Configuration Payload -> Cart Line Item Mapping

**Flow**: Finalized CPQ session -> Extract JSON config -> Create OrderItem with Config_Metadata\_\_c

```apex
public class {{projectPrefix}}_CPQCartService {
    public void addConfiguredProductToCart(String cartId, CPQSessionDTO session) {
        // Extract finalized configuration
        Map<String, Object> configuration = session.configurationData;

        // Create cart item
        OrderItem cartItem = new OrderItem(
            OrderId = (Id)cartId,
            Product2Id = (Id)session.productId,
            Quantity = (Integer)session.quantity,
            UnitPrice = session.totalPrice
        );
        insert cartItem;

        // Store configuration as metadata
        Order_Item_Config__c config = new Order_Item_Config__c(
            Order_Item__c = cartItem.Id,
            Configuration_JSON__c = JSON.serialize(configuration),
            Session_Id__c = session.sessionId
        );
        insert config;

        // Track for order submission
        {{projectPrefix}}_OrderConfigService.trackConfiguration(cartItem.Id, configuration);
    }
}
```

### Image Asset Management for Configured Products

```apex
public class {{projectPrefix}}_ConfiguredProductImageService {
    public String getConfigurationVisualization(String configMetadataId) {
        // Query stored configuration
        Order_Item_Config__c config = [
            SELECT Configuration_JSON__c FROM Order_Item_Config__c WHERE Id = :configMetadataId
        ];

        Map<String, Object> configMap = (Map<String, Object>)JSON.deserializeUntyped(config.Configuration_JSON__c);

        // Build image URL based on attributes
        String baseUrl = '/sfc/servlet.shepherd/version/download/';
        String imageKey = buildImageKey(configMap);

        ContentVersion cv = [
            SELECT Id FROM ContentVersion
            WHERE Title = :imageKey AND IsLatest = true
            LIMIT 1
        ];

        return baseUrl + cv.Id;
    }

    private String buildImageKey(Map<String, Object> config) {
        // E.g., "product-red-large-with-options"
        String key = (String)config.get('productBase');
        key += '-' + (String)config.get('color');
        key += '-' + (String)config.get('size');
        if ((Boolean)config.get('hasOptions')) {
            key += '-with-options';
        }
        return key;
    }
}
```

## Tax Provider Integration

The tax provider calculates sales tax for B2B orders with exemption handling and address validation.

### B2B Commerce Tax Calculator Extension

**Extension Hook**: Implements `ConnectApi.CartItemTaxExtension`

```apex
public class {{projectPrefix}}_TaxExtension implements ConnectApi.CartItemTaxExtension {
    public void extendCart(ConnectApi.CartExtensionInput input) {
        List<ConnectApi.CartItem> cartItems = input.getCartItems();
        String shippingAddress = input.getShippingAddressId();

        {{projectPrefix}}_TaxService taxService = new {{projectPrefix}}_TaxService();

        // Get account exemption status
        Account account = [SELECT Id, Tax_Exempt__c, Exemption_Cert_Number__c FROM Account WHERE Id = :input.getAccountId()];

        // Calculate tax for entire cart
        TaxResultDTO taxResult = taxService.calculateTax(cartItems, shippingAddress, account);

        // Apply tax to items
        Decimal totalTax = 0;
        Decimal productTax = taxResult.productTax;
        Decimal shippingTax = taxResult.shippingTax;

        for (ConnectApi.CartItem item : cartItems) {
            if (item.getProductId() != null) {
                item.setTaxAmount(productTax / cartItems.size());
                totalTax += item.getTaxAmount();
            }
        }

        // Add shipping tax as separate line
        input.setShippingTaxAmount(shippingTax);
    }
}
```

### Tax Calculation with Product + Shipping as Separate Lines

```apex
public class {{projectPrefix}}_TaxService {
    public TaxResultDTO calculateTax(List<ConnectApi.CartItem> items, String shippingAddress, Account account) {
        // Build tax transaction request
        String payload = buildTaxRequest(items, shippingAddress, account);

        // Call Tax Provider API
        HttpResponse response = callTaxAPI('POST', '/api/v2/transactions/create', payload);

        // Parse response
        TaxResponseDTO taxResponse = (TaxResponseDTO)JSON.deserialize(
            response.getBody(), TaxResponseDTO.class
        );

        TaxResultDTO result = new TaxResultDTO();
        result.productTax = 0;
        result.shippingTax = 0;

        // Sum taxes by line type
        for (TaxLineDTO line : taxResponse.lines) {
            if (line.lineType == 'PRODUCT') {
                result.productTax += line.tax;
            } else if (line.lineType == 'SHIPPING') {
                result.shippingTax += line.tax;
            }
        }

        return result;
    }

    private String buildTaxRequest(List<ConnectApi.CartItem> items, String shippingAddress, Account account) {
        Map<String, Object> request = new Map<String, Object>{
            'companyCode' => '{{projectPrefix}}',
            'type' => 'SalesOrder',
            'date' => Date.today().format(),
            'customerCode' => account.Id,
            'addresses' => new Map<String, Object>{
                'shipTo' => parseAddress(shippingAddress)
            },
            'lines' => new List<Map<String, Object>>(),
            'exemptionNo' => account.Exemption_Cert_Number__c
        };

        Integer lineNum = 1;
        List<Map<String, Object>> lines = (List<Map<String, Object>>)request.get('lines');

        // Add product lines
        for (ConnectApi.CartItem item : items) {
            lines.add(new Map<String, Object>{
                'number' => String.valueOf(lineNum++),
                'description' => item.getProductName(),
                'quantity' => item.getQuantity(),
                'amount' => item.getPrice() * item.getQuantity(),
                'lineType' => 'PRODUCT'
            });
        }

        // Add shipping line
        if (request.containsKey('shipping')) {
            lines.add(new Map<String, Object>{
                'number' => String.valueOf(lineNum++),
                'description' => 'Shipping',
                'amount' => (Decimal)request.get('shipping'),
                'lineType' => 'SHIPPING'
            });
        }

        return JSON.serialize(request);
    }
}
```

### Exemption Certificate Handling

```apex
public class {{projectPrefix}}_ExemptionCertificateService {
    public void updateExemptionStatus(Id accountId, String exemptionNumber, String exemptionType) {
        // Validate certificate with tax provider
        {{projectPrefix}}_TaxService taxService = new {{projectPrefix}}_TaxService();
        Boolean isValid = taxService.validateExemption(exemptionNumber, exemptionType);

        if (isValid) {
            Account account = new Account(Id = accountId);
            account.Tax_Exempt__c = true;
            account.Exemption_Cert_Number__c = exemptionNumber;
            account.Exemption_Type__c = exemptionType;
            update account;
        } else {
            throw new {{projectPrefix}}_ValidationException('Invalid exemption certificate');
        }
    }
}
```

### Address Validation

```apex
public class {{projectPrefix}}_AddressValidationService {
    public AddressValidationDTO validateAddress(String street, String city, String state, String zip) {
        Map<String, Object> request = new Map<String, Object>{
            'line1' => street,
            'city' => city,
            'region' => state,
            'postalCode' => zip,
            'country' => 'US'
        };

        HttpResponse response = callTaxAPI('POST', '/api/v2/addresses/validate', JSON.serialize(request));
        AddressValidationDTO result = (AddressValidationDTO)JSON.deserialize(response.getBody(), AddressValidationDTO.class);

        return result;
    }
}
```

## Middleware Patterns

The middleware layer connects Salesforce B2B, ERP, CPQ, and external services.

### API-Led Connectivity (System, Process, Experience Layers)

**System Layer**: Connects to backend systems (ERP, tax provider, payment gateways)

- Endpoints: `/system/erp/*`, `/system/tax/*`, `/system/payment/*`
- Authentication: OAuth 2.0 with Named Credentials
- Error handling: Transform errors to standard format

**Process Layer**: Orchestrates business logic and routing

- Endpoints: `/process/order-sync`, `/process/pricebook-sync`, `/process/tax-calc`
- Transformation: Map SF -> ERP formats, validate payloads
- Routing: Conditional flows based on order type, customer, region

**Experience Layer**: Exposes APIs to Salesforce front-end

- Endpoints: `/experience/checkout/*`, `/experience/products/*`, `/experience/orders/*`
- Rate limiting: 1000 req/minute per user
- Caching: 5-minute cache on product/pricebook endpoints

### Error Handling and Retry

```apex
public class {{projectPrefix}}_MiddlewareIntegration {
    public HttpResponse callMiddlewareAPI(String endpoint, String method, String payload) {
        HttpRequest req = new HttpRequest();
        req.setEndpoint('callout:Middleware_API' + endpoint);
        req.setMethod(method);
        req.setHeader('Content-Type', 'application/json');
        req.setHeader('X-Correlation-Id', generateCorrelationId());
        req.setTimeout(30000);

        if (payload != null) {
            req.setBody(payload);
        }

        return executeWithRetry(req, 0);
    }

    private HttpResponse executeWithRetry(HttpRequest req, Integer attempt) {
        if (attempt > 3) {
            throw new {{projectPrefix}}_IntegrationException('Max retries exceeded for ' + req.getEndpoint());
        }

        try {
            Http http = new Http();
            HttpResponse response = http.send(req);

            // Success
            if (response.getStatusCode() >= 200 && response.getStatusCode() < 300) {
                return response;
            }

            // Transient error (5xx, timeout) - retry
            if (response.getStatusCode() >= 500 || response.getStatusCode() == 408) {
                Integer delay = (Integer)Math.pow(2, attempt) * 1000; // 1s, 2s, 4s
                System.debug('Retrying in ' + delay + 'ms');
                return executeWithRetry(req, attempt + 1);
            }

            // Permanent error (4xx) - fail immediately
            throw new {{projectPrefix}}_IntegrationException('Middleware error: ' + response.getStatusCode() + ' ' + response.getStatus());

        } catch (System.CalloutException e) {
            // Network timeout/error - retry
            if (attempt < 3) {
                Integer delay = (Integer)Math.pow(2, attempt) * 1000;
                return executeWithRetry(req, attempt + 1);
            }
            throw new {{projectPrefix}}_IntegrationException('HTTP callout failed: ' + e.getMessage());
        }
    }
}
```

### Idempotency Keys for Order Creation

Prevent duplicate order creation if requests are retried:

```apex
public class {{projectPrefix}}_OrderSubmissionService {
    public String submitOrderToERP(Id sfOrderId) {
        Order order = [SELECT Id, Name, CreatedDate FROM Order WHERE Id = :sfOrderId];

        // Generate unique idempotency key
        String idempotencyKey = 'SF-' + order.Id + '-' + order.CreatedDate.getTime();

        Map<String, Object> payload = buildOrderPayload(order);

        HttpResponse response = new {{projectPrefix}}_MiddlewareIntegration().callMiddlewareAPI(
            '/process/order-sync',
            'POST',
            JSON.serialize(payload),
            idempotencyKey
        );

        // Extract ERP order ID from response
        Map<String, Object> result = (Map<String, Object>)JSON.deserializeUntyped(response.getBody());
        String erpOrderId = (String)result.get('orderId');

        // Store idempotency key for future reference
        Order updated = new Order(Id = sfOrderId);
        updated.Idempotency_Key__c = idempotencyKey;
        updated.ERP_Order_Number__c = (String)result.get('orderNumber');
        update updated;

        return erpOrderId;
    }
}
```

### Circuit Breaker Configuration

Gracefully degrade when middleware/ERP is down:

```apex
public class {{projectPrefix}}_CircuitBreaker {
    private static final String BREAKER_STATE_KEY = 'Middleware_CircuitBreaker';
    private static final Integer FAILURE_THRESHOLD = 5;
    private static final Integer TIMEOUT_MINUTES = 15;

    public static Boolean isOpen() {
        {{projectPrefix}}_CircuitBreakerState__c state = {{projectPrefix}}_CircuitBreakerState__c.getInstance(BREAKER_STATE_KEY);
        if (state == null) {
            return false;
        }

        if (state.Last_Failure__c == null) {
            return false;
        }

        Long timeSinceLastFailure = DateTime.now().getTime() - state.Last_Failure__c.getTime();
        Long timeoutMs = TIMEOUT_MINUTES * 60 * 1000;

        // Half-open: Check if timeout has passed
        if (state.Consecutive_Failures__c >= FAILURE_THRESHOLD && timeSinceLastFailure > timeoutMs) {
            return false; // Allow test call
        }

        return state.Consecutive_Failures__c >= FAILURE_THRESHOLD;
    }

    public static void recordSuccess() {
        {{projectPrefix}}_CircuitBreakerState__c state = {{projectPrefix}}_CircuitBreakerState__c.getInstance(BREAKER_STATE_KEY);
        if (state == null) {
            state = new {{projectPrefix}}_CircuitBreakerState__c(Name = BREAKER_STATE_KEY);
        }
        state.Consecutive_Failures__c = 0;
        upsert state;
    }

    public static void recordFailure() {
        {{projectPrefix}}_CircuitBreakerState__c state = {{projectPrefix}}_CircuitBreakerState__c.getInstance(BREAKER_STATE_KEY);
        if (state == null) {
            state = new {{projectPrefix}}_CircuitBreakerState__c(Name = BREAKER_STATE_KEY);
        }
        state.Consecutive_Failures__c = (state.Consecutive_Failures__c == null ? 0 : state.Consecutive_Failures__c) + 1;
        state.Last_Failure__c = DateTime.now();
        upsert state;
    }
}
```

### Monitoring and Alerting

All integration calls logged to `Integration_Log__c`:

```apex
public class {{projectPrefix}}_IntegrationLogger {
    public static void logCall(String service, String endpoint, String method, String payload, Integer statusCode, String response, Long durationMs) {
        Integration_Log__c log = new Integration_Log__c(
            Service__c = service,
            Endpoint__c = endpoint,
            Method__c = method,
            Payload__c = truncate(payload, 32000),
            Response__c = truncate(response, 32000),
            Status_Code__c = String.valueOf(statusCode),
            Duration_Ms__c = durationMs,
            Timestamp__c = DateTime.now()
        );
        insert log;

        // Alert on failures
        if (statusCode >= 400) {
            publishIntegrationErrorEvent(service, endpoint, statusCode, response);
        }
    }

    private static void publishIntegrationErrorEvent(String service, String endpoint, Integer statusCode, String response) {
        {{projectPrefix}}_IntegrationErrorEvent__e event = new {{projectPrefix}}_IntegrationErrorEvent__e(
            Service__c = service,
            Endpoint__c = endpoint,
            Status_Code__c = statusCode,
            Error_Message__c = truncate(response, 512),
            Occurred_At__c = DateTime.now()
        );
        EventBus.publish(event);
    }
}
```

## Payment Gateway Integration

### Abstract PaymentProcessor Interface

```apex
public interface PaymentProcessor {
    PaymentResult processPayment(PaymentRequest request);
    PaymentResult refundPayment(String transactionId, Decimal amount);
    PaymentResult voidPayment(String transactionId);
    PaymentTokenDTO tokenizeCard(CardDetails card);
}
```

### Primary Gateway Implementation Pattern

```apex
public class {{projectPrefix}}_PrimaryPaymentProcessor implements PaymentProcessor {
    private String apiKey = 'callout:Payment_API';

    public PaymentResult processPayment(PaymentRequest request) {
        // Create payment intent
        Map<String, Object> payload = new Map<String, Object>{
            'amount' => (Integer)(request.amount * 100), // Minor units (cents)
            'currency' => 'usd',
            'payment_method' => request.paymentMethodId,
            'confirmation_method' => 'manual',
            'confirm' => true,
            'off_session' => true,
            'metadata' => new Map<String, String>{
                'order_id' => request.orderId,
                'customer_id' => request.customerId
            }
        };

        try {
            HttpResponse response = callPaymentAPI('POST', '/v1/payment_intents', JSON.serialize(payload));
            PaymentIntentDTO intent = parsePaymentResponse(response.getBody());

            return new PaymentResult(
                'PrimaryGateway',
                intent.id,
                intent.status,
                request.amount,
                true,
                null
            );
        } catch (Exception e) {
            return new PaymentResult(
                'PrimaryGateway',
                null,
                'failed',
                request.amount,
                false,
                e.getMessage()
            );
        }
    }

    public PaymentResult refundPayment(String transactionId, Decimal amount) {
        Map<String, Object> payload = new Map<String, Object>{
            'payment_intent' => transactionId,
            'amount' => (Integer)(amount * 100)
        };

        HttpResponse response = callPaymentAPI('POST', '/v1/refunds', JSON.serialize(payload));
        return parseRefundResponse(response.getBody());
    }

    public PaymentTokenDTO tokenizeCard(CardDetails card) {
        // Create payment method token
        Map<String, Object> payload = buildCardPayload(card);
        HttpResponse response = callPaymentAPI('POST', '/v1/payment_methods', JSON.serialize(payload));
        PaymentMethodDTO method = parsePaymentMethodResponse(response.getBody());

        return new PaymentTokenDTO(method.id, method.card.last4, method.card.exp_month, method.card.exp_year);
    }
}
```

### Secondary Gateway Implementation Pattern

```apex
public class {{projectPrefix}}_SecondaryPaymentProcessor implements PaymentProcessor {
    private String apiKey = 'callout:Secondary_Payment_API';

    public PaymentResult processPayment(PaymentRequest request) {
        Map<String, Object> payload = new Map<String, Object>{
            'merchantAccount' => '{{projectPrefix}}_Merchant',
            'amount' => new Map<String, Object>{
                'value' => (Long)(request.amount * 100), // Minor units
                'currency' => 'USD'
            },
            'paymentMethod' => new Map<String, Object>{
                'type' => 'scheme',
                'encryptedCardNumber' => request.paymentMethodId // Pre-encrypted
            },
            'reference' => request.orderId
        };

        try {
            HttpResponse response = callSecondaryAPI('POST', '/payments', JSON.serialize(payload));
            SecondaryPaymentResponseDTO paymentResponse = parseSecondaryResponse(response.getBody());

            Boolean isSuccess = paymentResponse.resultCode == 'Authorised';
            return new PaymentResult(
                'SecondaryGateway',
                paymentResponse.pspReference,
                paymentResponse.resultCode,
                request.amount,
                isSuccess,
                isSuccess ? null : paymentResponse.refusalReason
            );
        } catch (Exception e) {
            return new PaymentResult('SecondaryGateway', null, 'Failed', request.amount, false, e.getMessage());
        }
    }
}
```

### PCI Compliance (Tokenization, No CC Storage)

**Critical**: Never store credit card data in Salesforce

```apex
public class {{projectPrefix}}_PaymentTokenService {
    // Client-side: Generate token via payment gateway JavaScript library
    // Token is passed to Apex, never the raw card data

    public String savePaymentMethod(Id accountId, String paymentToken, String last4Digits, String expiry) {
        // Store only token reference and last 4 digits
        Payment_Method__c pm = new Payment_Method__c(
            Account__c = accountId,
            Token__c = paymentToken,
            Last_Four_Digits__c = last4Digits,
            Expiry__c = expiry,
            Is_Default__c = false,
            Created_At__c = DateTime.now()
        );
        insert pm;

        return pm.Id;
    }

    public void deletePaymentMethod(Id paymentMethodId) {
        // Remove token when customer deletes payment method
        delete [SELECT Id FROM Payment_Method__c WHERE Id = :paymentMethodId];
    }
}
```

### Refund and Void Flows

```apex
public class {{projectPrefix}}_RefundService {
    public void refundOrder(Id orderId, Decimal refundAmount) {
        Order order = [SELECT Id, ERP_Order_Number__c, Payment_Transaction_Id__c FROM Order WHERE Id = :orderId];

        // Get payment processor
        PaymentProcessor processor = getPaymentProcessor(order.Payment_Provider__c);

        // Process refund
        PaymentResult result = processor.refundPayment(order.Payment_Transaction_Id__c, refundAmount);

        if (result.success) {
            // Create refund record
            Order_Refund__c refund = new Order_Refund__c(
                Order__c = orderId,
                Amount__c = refundAmount,
                Transaction_Id__c = result.transactionId,
                Status__c = 'Processed',
                Refunded_At__c = DateTime.now()
            );
            insert refund;

            // Update order status
            Order updated = new Order(Id = orderId);
            updated.Status = 'Refunded';
            update updated;
        } else {
            throw new {{projectPrefix}}_IntegrationException('Refund failed: ' + result.errorMessage);
        }
    }

    public void voidPayment(Id orderId) {
        Order order = [SELECT Id, Payment_Transaction_Id__c FROM Order WHERE Id = :orderId];
        PaymentProcessor processor = getPaymentProcessor(order.Payment_Provider__c);

        PaymentResult result = processor.voidPayment(order.Payment_Transaction_Id__c);

        if (result.success) {
            Order updated = new Order(Id = orderId);
            updated.Status = 'Voided';
            updated.Payment_Voided_At__c = DateTime.now();
            update updated;
        }
    }
}
```

### Reverse Surcharge Handling

Handle payment surcharges (convenience fees) for certain payment methods:

```apex
public class {{projectPrefix}}_SurchargeCalculator {
    public Decimal calculateSurcharge(String paymentMethod, Decimal orderAmount) {
        // Map payment method to surcharge percentage
        Map<String, Decimal> surchargeRates = new Map<String, Decimal>{
            'credit_card' => 0.025, // 2.5%
            'debit_card' => 0.010,  // 1%
            'ach' => 0,             // No surcharge
            'wire_transfer' => 0    // No surcharge
        };

        Decimal rate = surchargeRates.get(paymentMethod.toLowerCase());
        if (rate == null) {
            return 0;
        }

        return orderAmount * rate;
    }

    public Decimal calculateReverseSurcharge(String paymentMethod, Decimal surchargeAmount) {
        // If customer pays via ACH instead of credit card, reverse surcharge
        return surchargeAmount; // Return amount to credit back
    }
}
```

## Data Flow Diagrams (Text-Based)

### Checkout Flow

```
Customer Reviews Cart
    |
LWC: {{projectPrefix}}_CheckoutComponent
    |
Validate Cart Items (Inventory Extension)
    |
Calculate Pricing (Pricing Extension)
    |
Calculate Tax (Tax Extension via Tax Provider)
    |
Calculate Shipping (Shipping Extension)
    |
Select Payment Method
    |
Tokenize Card (Payment Gateway on Client)
    |
Submit Order ({{projectPrefix}}_CheckoutController -> {{projectPrefix}}_OrderService)
    |
Create SF Order + Payment_Transaction__c
    |
CheckoutSessionAction Trigger
    |
Call Middleware: Create ERP Sales Order
    |
Middleware -> ERP API
    |
Update SF Order with ERP Order Number
    |
Publish {{projectPrefix}}_OrderCreatedEvent__e
    |
Subscribe: Send Confirmation Email + Notify Warehouse
    |
Order Complete
```

### Order Status Sync Flow

```
ERP Order Status Changes
    |
ERP -> Middleware (Polling or Webhook)
    |
Middleware transforms status
    |
Middleware POST to SF Experience API: /experience/orders/{orderId}/status-sync
    |
{{projectPrefix}}_ERPStatusSyncHandler (Queueable)
    |
Update SF Order.Status
    |
Publish {{projectPrefix}}_OrderStatusChangeEvent__e
    |
Subscribe: Update Cart UI, Send Email Notification
    |
Customer sees updated status in portal
```

### Pricebook Sync Flow

```
2:00 AM CST Daily
    |
Scheduled Job: {{projectPrefix}}_PricebookSyncSchedulable
    |
Execute Batch: {{projectPrefix}}_PricebookSyncBatch
    |
Query Products modified in last 24 hours
    |
Batch 1-200: Call Middleware /system/erp/prices?filter=lastModified>24h
    |
Middleware -> ERP Product Master API
    |
Upsert 200 PricebookEntry records
    |
Batch 2-200: Repeat...
    |
Finish: Publish {{projectPrefix}}_PricebookSyncCompleteEvent__e
    |
Update Last_Sync_Time__c = now()
    |
Notify if errors > threshold
```

### Product Sync Flow

```
ERP Product Master Updated
    |
Middleware: Webhook -> SF Experience API: /experience/products/sync
    |
{{projectPrefix}}_ERPProductSyncService (Queueable)
    |
Upsert Product2 by External_Product_Id__c
    |
Extract/Create PricebookEntries
    |
Update ContentVersion for Product Images
    |
Publish {{projectPrefix}}_ProductSyncCompleteEvent__e
    |
Subscribers: Refresh product catalog cache
    |
Customers see updated products in search/browse
```

### Tax Calculation Flow

```
Customer at Checkout
    |
Cart Extension: Tax Extension triggered
    |
{{projectPrefix}}_TaxExtension.extendCart()
    |
Query Account for exemption status
    |
Build tax TransactionCreate request
    |
Call Tax Provider API: POST /transactions/create
    |
Tax Provider: Apply rules, jurisdictions, exemptions
    |
Response: Product tax ($100), Shipping tax ($8)
    |
Apply tax lines to cart
    |
Display updated total to customer ($1,108)
```

### Payment Processing Flow

```
Customer clicks "Pay Now"
    |
Payment form (gateway hosted)
    |
Client: Tokenize card -> Get token
    |
Submit PaymentRequest with token (NOT card data)
    |
{{projectPrefix}}_PaymentService.processPayment()
    |
Get PaymentProcessor (primary or secondary gateway)
    |
Call processor.processPayment(token, amount)
    |
Processor API: Charge card
    |
Response: Success + Transaction ID OR Failure + Reason
    |
If success: Update Order.Payment_Status__c = 'Authorized'
    |
If success: Create Payment_Transaction__c record
    |
If failure: Show error to customer, allow retry
    |
Complete checkout
```

### CPQ Configuration Flow

```
Customer selects "Configure Product"
    |
LWC: {{projectPrefix}}_CPQConfigurationComponent
    |
Create CPQ Blueprint session
    |
{{projectPrefix}}_CPQService.createBlueprint(blueprintId)
    |
Call CPQ: POST /blueprints/{id}/session
    |
Get SessionId + Available attributes
    |
Display configuration UI with swatches, dimensions
    |
Customer selects options
    |
Update CPQ session: updateBlueprint(sessionId, {attributes})
    |
Customer clicks "Finalize"
    |
Call CPQ: finalizeBlueprint(sessionId)
    |
Get configuration JSON + Image URL
    |
Add to Cart as configured item
    |
Store config in Order_Item_Config__c
    |
Cart display: Show configured product image + attributes
```

## Error Recovery

### Dead Letter Queues

Store failed payloads for manual inspection and retry:

```apex
public class {{projectPrefix}}_DeadLetterQueueService {
    public static void enqueue(String service, String payload, String errorMessage) {
        Dead_Letter_Queue__c dlq = new Dead_Letter_Queue__c(
            Service__c = service,
            Payload__c = payload,
            Error_Message__c = errorMessage,
            Status__c = 'Pending',
            Retry_Count__c = 0,
            Enqueued_At__c = DateTime.now()
        );
        insert dlq;
    }

    public static void retry(Id dlqId) {
        Dead_Letter_Queue__c dlq = [SELECT Id, Service__c, Payload__c FROM Dead_Letter_Queue__c WHERE Id = :dlqId];

        try {
            if (dlq.Service__c == 'ERP') {
                new {{projectPrefix}}_ERPIntegration().createSalesOrder(dlq.Payload__c);
            }
            dlq.Status__c = 'Resolved';
            dlq.Resolved_At__c = DateTime.now();
        } catch (Exception e) {
            dlq.Retry_Count__c = dlq.Retry_Count__c + 1;
            dlq.Last_Error__c = e.getMessage();
            if (dlq.Retry_Count__c > 5) {
                dlq.Status__c = 'Failed';
            }
        }

        update dlq;
    }
}
```

### Manual Retry Mechanisms

UI component for support team to retry failed syncs:

```apex
public class {{projectPrefix}}_ManualRetryController {
    @AuraEnabled
    public static void retryIntegration(String integrationLogId) {
        Integration_Log__c log = [SELECT Id, Service__c, Payload__c FROM Integration_Log__c WHERE Id = :integrationLogId];

        if (log.Service__c == 'ERP') {
            {{projectPrefix}}_ERPIntegration integration = new {{projectPrefix}}_ERPIntegration();
            integration.createSalesOrder(log.Payload__c);
        } else if (log.Service__c == 'Tax') {
            // Retry tax calculation
        }
    }
}
```

### Reconciliation Patterns

Verify data consistency between Salesforce and ERP:

```apex
public class {{projectPrefix}}_ReconciliationService {
    public void reconcileOrders(Date syncDate) {
        // Find all SF orders created on syncDate
        List<Order> sfOrders = [
            SELECT Id, ERP_Order_Number__c FROM Order
            WHERE CreatedDate = :syncDate
        ];

        // Query ERP for orders created on syncDate
        List<ERPOrderDTO> erpOrders = callERPAPI('/orders?createdDate=' + syncDate);

        // Compare order counts
        if (sfOrders.size() != erpOrders.size()) {
            publishReconciliationAlert('Order count mismatch on ' + syncDate);
        }

        // Check for missing ERP order numbers
        Set<String> erpOrderNums = new Set<String>();
        for (ERPOrderDTO eo : erpOrders) {
            erpOrderNums.add(eo.orderNumber);
        }

        for (Order so : sfOrders) {
            if (!erpOrderNums.contains(so.ERP_Order_Number__c)) {
                publishReconciliationAlert('SF order ' + so.Id + ' missing ERP number');
            }
        }
    }
}
```

---

## Integration Architecture Summary

- **Middleware** orchestrates all system-to-system calls (ERP, tax provider, payment gateway)
- **Named Credentials** manage all API authentication securely
- **Circuit Breakers** protect from cascading failures
- **Idempotency Keys** prevent duplicate orders on retry
- **Platform Events** enable async, decoupled communication
- **Dead Letter Queues** capture and surface failed integrations
- **Structured Logging** enables monitoring and debugging

All integrations follow retry (3x with exponential backoff), timeout (30s), and circuit breaker patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/architect-and-bot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
