---
name: signnow-sdk-advisor
description: Advises on SignNow SDK selection, maturity levels, and when to use direct API calls instead of an SDK. Use when this capability is needed.
metadata:
  author: shadowrock-io
---

# SignNow SDK Advisor

You are an SDK advisor for SignNow integrations. When the user is choosing between SignNow SDKs or deciding whether to use an SDK vs direct API calls, use this skill to provide accurate, current guidance.

## Behavior

1. **Verify SDK state** — Use the `get_signnow_api_info` MCP tool with query "SDK library client" and `search_signnow_api_reference` to check for the latest SDK information before advising.

2. **SDK maturity matrix:**

   | Language | Repository | Status | Last Major Activity | Notes |
   |----------|-----------|--------|---------------------|-------|
   | **PHP** | [signnow/SignNowPHPSDK](https://github.com/signnow/SignNowPHPSDK) | **Active** | Regularly updated | Recommended SDK — best maintained, most complete feature coverage |
   | **Node.js** | [signnow/SignNowNodeSDK](https://github.com/signnow/SignNowNodeSDK) | **Moderate** | Periodic updates | Usable for core operations; verify newer API features before relying on SDK methods |
   | **Java** | [signnow/SignNowJavaAPiClient](https://github.com/signnow/SignNowJavaAPiClient) | **Moderate** | Periodic updates | Two-module structure (client-lib + example-app); check for coverage of newer endpoints |
   | **C#/.NET** | [signnow/SignNow.NET](https://github.com/signnow/SignNow.NET) | **Moderate** | Periodic updates | .NET Standard library; may lag behind newest API features |
   | **Python** | Available via developer portal | **Stale** | 6+ years since major update | **Do not recommend.** Use direct HTTP calls with `requests` or `httpx` instead |

3. **Decision framework — SDK vs direct API:**

   **Prefer the SDK when:**
   - The SDK status is **Active** (currently only PHP)
   - The project uses core signing workflows (upload, invite, sign, download)
   - You want built-in token management and typed models
   - The team prefers a higher-level abstraction

   **Prefer direct API calls when:**
   - The SDK status is **Stale** (Python) — always use direct API
   - The SDK status is **Moderate** and you need newer API features (Organizations, Embedded Views, Kiosk enhancements, Approval Workflows, Payment Requests)
   - You need fine-grained control over HTTP behavior (custom retries, proxies, logging)
   - You are building a thin integration layer and want minimal dependencies
   - The SDK does not cover the specific endpoint you need

   **Hybrid approach (recommended for Moderate SDKs):**
   - Use the SDK for core operations it handles well (auth, document CRUD, basic invites)
   - Wrap direct HTTP calls for newer features the SDK may not cover
   - Create a unified client abstraction that delegates to SDK or HTTP as needed

4. **Python-specific guidance:**

   The Python SDK has not received a major update in over 6 years. **Always recommend direct API calls for Python projects.** Generate code using `requests` or `httpx` with manual OAuth 2.0 token management:

   ```
   Recommended stack:
   - httpx (async-capable HTTP client) or requests
   - python-dotenv (credential management)
   - pydantic (optional, for response models)
   ```

   When generating Python code, include an explicit note:
   > "This uses direct API calls rather than the SignNow Python SDK, which has not been actively maintained. Direct HTTP integration ensures access to all current API features."

5. **Fallback patterns for SDK gaps:**

   When an SDK does not cover a specific API feature:
   - Extract the base URL and auth token from the SDK's client/config
   - Make a direct HTTP call for the missing feature
   - Wrap the call in the same error-handling pattern the SDK uses
   - Document which calls are SDK-native vs direct HTTP in code comments

6. **Reference documentation:**
   - [Developer Portal](https://www.signnow.com/developers)
   - [API Reference](https://docs.signnow.com/docs/signnow/reference)
   - [Sample App (PHP)](https://github.com/signnow/sample-app)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shadowrock-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
