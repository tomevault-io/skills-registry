---
name: rest-http-caller
description: Make HTTP/REST API calls using a C# script. Use this skill when you need to call REST APIs, fetch data from web services, send POST/PUT/DELETE requests, or test HTTP endpoints. Supports all HTTP methods, custom headers, request bodies, and authentication. Use when this capability is needed.
metadata:
  author: mattgotteiner
---

# REST HTTP Caller Skill

This skill enables you to make HTTP/REST API calls using a simple C# script that runs with `dotnet run`.

## Prerequisites

- .NET 10 or later installed (for `dotnet run app.cs` support)
- Network access to the target endpoint

## Usage

Run the HTTP caller script using `dotnet run`:

```bash
dotnet run HttpCaller.cs -- <METHOD> <URL> [OPTIONS]
```

### Arguments

| Argument | Required | Description |
|----------|----------|-------------|
| `METHOD` | Yes | HTTP method: GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS |
| `URL` | Yes | The full URL to call (must include scheme, e.g., https://) |

### Options

| Option | Description | Example |
|--------|-------------|---------|
| `--header` or `-H` | Add a header (can be used multiple times) | `--header "Authorization: Bearer token123"` |
| `--body` or `-b` | Request body (JSON string) | `--body '{"name": "test"}'` |
| `--body-file` or `-f` | Read request body from a file | `--body-file request.json` |
| `--timeout` or `-t` | Request timeout in seconds (default: 30) | `--timeout 60` |
| `--output` or `-o` | Save response body to a file | `--output response.json` |
| `--verbose` or `-v` | Show detailed request/response info | `--verbose` |
| `--help` or `-?` | Show help and usage information | `--help` |

## Examples

### GET Request

```bash
dotnet run HttpCaller.cs -- GET https://api.example.com/users
```

### GET with Authentication Header

```bash
dotnet run HttpCaller.cs -- GET https://api.example.com/users --header "Authorization: Bearer YOUR_TOKEN_HERE"
```

### POST with JSON Body

```bash
dotnet run HttpCaller.cs -- POST https://api.example.com/users --header "Content-Type: application/json" --body '{"name": "John Doe", "email": "john@example.com"}'
```

### PUT Request with Body from File

```bash
dotnet run HttpCaller.cs -- PUT https://api.example.com/users/123 --header "Content-Type: application/json" --body-file update-user.json
```

### DELETE Request

```bash
dotnet run HttpCaller.cs -- DELETE https://api.example.com/users/123 --header "Authorization: Bearer YOUR_TOKEN_HERE"
```

### Multiple Headers

```bash
dotnet run HttpCaller.cs -- POST https://api.example.com/data --header "Content-Type: application/json" --header "Authorization: Bearer TOKEN" --header "X-Custom-Header: value" --body '{"data": "test"}'
```

### Verbose Output with Timeout

```bash
dotnet run HttpCaller.cs -- GET https://api.example.com/slow-endpoint --timeout 120 --verbose
```

### Save Response to File

```bash
dotnet run HttpCaller.cs -- GET https://api.example.com/large-data --output data.json
```

## Script Location

The HTTP caller script is located at: [HttpCaller.cs](./HttpCaller.cs)

## Output Format

The script outputs:
- **Status**: HTTP status code and reason phrase
- **Headers**: Response headers (in verbose mode)
- **Body**: Response body (JSON is automatically formatted)

### Example Output

```
=== HTTP Response ===
Status: 200 OK

Body:
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com"
}
```

### Verbose Output

```
=== HTTP Request ===
Method: POST
URL: https://api.example.com/users
Headers:
  Content-Type: application/json
  Authorization: Bearer ***
Body:
{"name": "John Doe", "email": "john@example.com"}

=== HTTP Response ===
Status: 201 Created
Headers:
  Content-Type: application/json
  X-Request-Id: abc123
Body:
{
  "id": 456,
  "name": "John Doe",
  "email": "john@example.com"
}
```

## Common Use Cases

1. **Testing APIs**: Quickly test REST endpoints during development
2. **Fetching Data**: Retrieve data from external services
3. **Automation**: Integrate API calls into workflows
4. **Debugging**: Use verbose mode to inspect request/response details
5. **Authentication Testing**: Test various auth mechanisms (Bearer, API Key, Basic)

## Error Handling

The script provides clear error messages for common issues:
- Network errors
- Invalid URLs
- Timeout errors
- HTTP error status codes (4xx, 5xx)

## Tips for LLM Usage

When using this skill, fill in:
1. **METHOD**: Choose the appropriate HTTP method for the operation
2. **URL**: Provide the complete endpoint URL
3. **Headers**: Add necessary headers like `Content-Type`, `Authorization`, etc.
4. **Body**: For POST/PUT/PATCH, provide the request body as JSON

Always include `Content-Type: application/json` header when sending JSON data.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mattgotteiner) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
