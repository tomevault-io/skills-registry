---
name: rest-api-design-ai-kit
description: REST API design standards and URI naming guidelines for .NET microservices. Use this skill whenever the user asks about REST API design, endpoint naming conventions, URI structure, HTTP method selection (GET, POST, PUT, DELETE, PATCH), or API route patterns. Specifically trigger for questions about plural vs singular resource names, using hyphens instead of underscores in URLs, avoiding trailing slashes, lowercase URI paths, no file extensions in routes, query string usage for filtering and pagination, resource hierarchy in URL paths, avoiding verbs in endpoint names, or exposing PII/sensitive data in URL paths or query strings. Also trigger when the user is designing a new controller, reviewing existing routes for consistency, or asking whether to put a parameter in the path, query string, or request body. Use when this capability is needed.
metadata:
  author: ducthang-hub
---


# REST API Design Standards

## URI Rules

### Rule #1: Don't expose Sensitive Data or PII

HTTP request routes and query strings are logged automatically by any APM (Application Performance Monitoring), so proper care must be taken. Be mindful of using GET vs POST, headers, or query string parameters.

✅ **MUST DO**
```
HTTP POST: http://api.example.com/user - put the username in the body
HTTP GET: http://api.example.com/user/{encryptedUsername} - Encrypt username if necessary
HTTP GET: http://api.example.com/user/{userGUID} - Consider a GUID rather than needing encryption
```

❌ **MUST NOT DO**
```
api.example.com/users/{username}
api.example.com/users/retrieve-all?username={username}
```

### Rule #2: No trailing forward slash (/)

REST APIs must not expect a trailing slash and must not include them in links provided to clients.

✅ **MUST DO**: `http://api.example.com/products`  
❌ **MUST NOT DO**: `http://api.example.com/products/`

### Rule #3: Use forward slash (/) for hierarchical relationships

The forward slash character indicates a hierarchical relationship between resources.

**Example**: `http://api.example.com/shapes/polygons/quadrilaterals/squares`

### Rule #4: Use hyphens (-) instead of underscores (_)

Underscores can be obscured by underlining in text viewers.

✅ **MUST DO**: `http://api.example.com/purchase-orders`  
❌ **MUST NOT DO**: `http://api.example.com/purchase_orders`

### Rule #5: Use lowercase letters in URI paths

Lowercase letters are preferred since capital letters can sometimes cause problems.

✅ **MUST DO**: `http://api.example.com/purchase-orders`  
❌ **MUST NOT DO**: `http://api.example.com/purchaseOrders`

### Rule #6: No file extensions in URIs

RESTful APIs must not include file extensions like `.json`.

✅ **MUST DO**: `http://api.college.com/students/3248234/courses/2005/fall`  
❌ **MUST NOT DO**: `http://api.college.com/students/3248234/courses/2005/fall.json`

Use the Accept header to control response format (XML, JSON, etc.).

### Rule #7: Endpoint names must be plural

Always use plural nouns for consistency. The URL describes a path to a resource collection.

✅ **MUST DO**: 
- `http://api.example.com/students/1/courses` - List all courses for student 1
- `http://api.example.com/students/1/courses/physics` - Get physics course for student 1

❌ **MUST NOT DO**: 
- `http://api.example.com/students/1/course`
- `http://api.example.com/categories/1/courses/physic`

### Rule #8: Prioritize nouns over verbs

Use nouns to represent entities since HTTP methods already provide verbs.

✅ **MUST DO**: 
- `HTTP GET: http://api.example.com/products` - Retrieve all products
- `HTTP DELETE: http://api.example.com/products/1` - Delete product 1

❌ **MUST NOT DO**: 
- `http://api.example.com/products/get`
- `http://api.example.com/products/1/delete`

### Rule #9: Use URL path for resource identification and relationships

Use URL path to represent resources, collections, and hierarchical relationships.

✅ **MUST DO**: `HTTP GET: http://api.example.com/users/123/orders/456`  
❌ **MUST NOT DO**: `HTTP GET: http://api.example.com/orders?userId=123&orderId=456`

### Rule #10: Use query strings for filtering and optional parameters

Use query strings for filters, search criteria, pagination, or sorting.

✅ **MUST DO**: `HTTP GET: http://api.example.com/products?category=electronics&page=2&sort=price`  
❌ **MUST NOT DO**: `HTTP GET: http://api.example.com/products/electronics/2/price`

**Important**: Don't violate Rules #1 and #12 - query strings are not suitable for sensitive data.

### Rule #11: Avoid user inputs in URL paths

Use properly encoded query strings for user-controlled data to avoid routing issues with spaces or special characters.

✅ **MUST DO**: `HTTP GET: http://api.example.com/fees/advice%20fees/paid`  
❌ **MUST NOT DO**: `HTTP GET: http://api.example.com/fees/paid?type=advice%20fees`

### Rule #12: Use request body for complex operations

Use the request body for:
- Creating new resources
- Updating existing resources  
- Complex searches with numerous parameters
- Bulk operations
- Sensitive data

## HTTP Methods

### POST - Create resource
Use POST requests to create resources.

### GET - Retrieve resource  
Use GET requests to retrieve data without modification. GET APIs should be idempotent.

**Note**: For GET requests with many parameters, consider using POST instead (see Rule 12).

### PUT / PATCH - Update resource
- **PUT**: Complete resource replacement
- **PATCH**: Partial resource updates

### DELETE - Delete resource
Use DELETE to remove existing resources.

## HTTP Status Codes

Use appropriate status codes for meaningful responses:

- **201 (Created)**: New resources successfully created via POST
- **202 (Accepted)**: Request queued for later processing
- **204 (No Content)**: Successful request with no response body
- **400 (Bad Request)**: Invalid syntax or input from client
- **401 (Unauthorized)**: JWT not present or invalid (Kong returns this)
- **403 (Forbidden)**: User lacks access to requested resource
- **404 (Not Found)**: Route doesn't exist (don't return for missing data to avoid information leakage)

### Kong Intercept (Shared Platform)
In shared platform workspaces, Kong intercepts and replaces 4xx status codes with 400 to prevent information leakage. Return status 200 or 4xx with response body for client-side validation handling.

## API Performance Guidelines

REST APIs should be short-lived:
- **Client-facing**: Return in <2 seconds
- **Warning threshold**: >5 seconds should raise alarms

For long-running operations, consider:
1. **Code optimisation**: Refactor for better performance
2. **Data structure optimisation**: Improve data access patterns  
3. **Caching**: Implement preprocessing or caching strategies
4. **Asynchronous patterns**: Convert to [async request/reply pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/async-request-reply)

## API Versioning

- All microservice APIs must have **Major.Minor** versioning (or Major only: v1, v2)
- Embed version in request URL path: `https://api.contoso.com/v1/products/users`
- DTOs/Models can be optionally versioned

### Versioning Example

```csharp
[FunctionName("GetFoo_v1")]
public static IActionResult GetFoo_v1(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "api/v1/devices")] HttpRequest req, 
    ILogger log)
{
    string requestBody = new StreamReader(req.Body).ReadToEnd();
    GetFooRequest_v1 request = JsonConvert.DeserializeObject<GetFooRequest_v1>(requestBody);
    Foo foo = GetFoo(request.Name, DateTime.MinValue); // Default value for startDate
    GetFooResponse_v1 response = new GetFooResponse_v1(foo.Name, foo.Age); // Ignore Description
    return new JsonResult(response);
}

[FunctionName("GetFoo_v2")]  
public static IActionResult GetFoo_v2(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "api/v2/devices")] HttpRequest req,
    ILogger log)
{
    string requestBody = new StreamReader(req.Body).ReadToEnd();
    GetFooRequest_v2 request = JsonConvert.DeserializeObject<GetFooRequest_v2>(requestBody);
    Foo foo = GetFoo(request.Name, request.StartDate); // v2 includes StartDate
    GetFooResponse_v2 response = new GetFooResponse_v2(foo.Name, foo.Age, foo.Description); // Include Description
    return new JsonResult(response);
}
```

### DTO Versioning Examples

```csharp
// v1 Response
class GetFooResponse_v1
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Size { get; set; }
}

// v2 Response - use inheritance when adding properties
class GetFooResponse_v2 : GetFooResponse_v1
{
    public string Description { get; set; }
}

// v3 Response - can't use inheritance when removing properties
class GetFooResponse_v3
{
    public int Id { get; set; }
    public string Name { get; set; }
    // Size property removed
}
```

## Example URL Patterns

```
GET: https://api.example.com/customers - Retrieve list of customers
GET: https://api.example.com/customers?age=30 - Filter customers by age
GET: https://api.example.com/customers/1 - Retrieve customer with ID 1
GET: https://api.example.com/customers/1/accounts - List accounts for customer 1
GET: https://api.example.com/customers/1/accounts/2 - Get account 2 for customer 1
GET: https://api.example.com/customers/1/accounts?type=test - Filter accounts by type
GET: https://api.example.com/accounts/{encryptedAccountNo} - Get account by encrypted number
GET: https://api.example.com/prices - Retrieve list of prices
GET: https://api.example.com/assets/{assetCode} - Get asset by code
GET: https://api.example.com/assets/{assetCode}/prices - Get prices for specific asset
```

## Best Practices

1. **Security first** - Never expose PII in URLs
2. **Consistency** - Follow naming conventions consistently
3. **Performance** - Keep APIs fast and responsive
4. **Documentation** - Provide clear API documentation
5. **Versioning** - Plan for API evolution from the start
6. **Error handling** - Return meaningful error responses
7. **Monitoring** - Track API performance and usage

## References

- [Software Versioning Guidelines](https://netwealth.atlassian.net/wiki/spaces/BS/pages/52618035)
- [Azure Serverless API Versioning](https://devblogs.microsoft.com/premier-developer/versioning-rest-apis-in-azure-serverless/)

## Built from

https://netwealth.atlassian.net/wiki/spaces/BS/pages/67600780

---
> Source: [ducthang-hub/nw-ai-kit](https://github.com/ducthang-hub/nw-ai-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
