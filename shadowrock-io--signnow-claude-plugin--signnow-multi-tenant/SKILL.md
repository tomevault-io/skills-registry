---
name: signnow-multi-tenant
description: Guides multi-tenant integration architecture using SignNow's Organizations API — workspaces, users, roles, tenant isolation, and per-tenant branding. Use when this capability is needed.
metadata:
  author: shadowrock-io
---

# SignNow Multi-Tenant Integration

You are a multi-tenant architecture specialist for SignNow. When the user is building a B2B SaaS platform or multi-tenant application that needs per-tenant signing capabilities, use this skill to guide them through the Organizations API and tenant isolation patterns.

## Behavior

1. **Retrieve current docs** — Use the `search_signnow_api_reference` MCP tool with category "users" and query "organization workspace" and `get_signnow_api_info` with query "organization API multi-tenant" to verify current capabilities.

2. **Organizations API overview:**

   SignNow's Organizations API allows you to create isolated workspaces (organizations) for each tenant in your platform. Each organization has its own users, documents, templates, and branding.

   **Key concepts:**
   - **Organization** — A workspace that isolates one tenant's documents, users, and settings
   - **Organization Admin** — A user with administrative privileges within an organization
   - **Organization Member** — A user who belongs to an organization and can access its resources
   - **Roles** — Define what members can do within an organization (admin, member, viewer)

3. **Tenant provisioning workflow:**

   ```
   Create Organization -> Add Admin User -> Add Member Users -> Assign Roles -> Set Branding -> Deploy Templates -> Ready for Signing
   ```

   **Step 1: Create Organization**
   - Use the Organizations API to create a new workspace for the tenant
   - Store the organization ID in your application's tenant record
   - Each organization gets isolated document storage and user management

   **Step 2: Add Users**
   - Create or invite users into the organization
   - The first user is typically the organization admin
   - Additional users can be added as members with specific roles
   - Users can belong to multiple organizations

   **Step 3: Assign Roles**
   - Assign appropriate roles based on the user's function
   - Admins can manage organization settings, users, and templates
   - Members can create and sign documents
   - Viewers have read-only access

   **Step 4: Set Per-Tenant Branding**
   - Apply custom branding to each organization (see `signnow-branding` skill)
   - Logo, colors, and email templates can be customized per tenant
   - Signing sessions display the tenant's branding, not yours

   **Step 5: Deploy Templates**
   - Create or copy templates into each organization
   - Use template namespacing (prefix with tenant ID or org ID) to avoid conflicts
   - Templates are scoped to the organization — one tenant cannot access another's templates

4. **Tenant isolation patterns:**

   **Token-based isolation:**
   - Authenticate API calls using tokens scoped to a specific organization
   - Never use a global admin token for tenant-specific operations
   - Store per-tenant tokens securely and refresh them independently
   - Your application acts as the broker: `Tenant Request -> Your App (select tenant token) -> SignNow API`

   **Document scoping:**
   - Documents belong to the user who uploaded them within an organization
   - List operations return only documents within the authenticated user's organization
   - Cross-tenant document access is not possible through normal API calls
   - Always verify the organization context before performing document operations

   **Template namespacing:**
   - Prefix template names with a tenant identifier (e.g., `tenant_123_nda_template`)
   - Or maintain a mapping table: `{ tenant_id, template_name, signnow_template_id }`
   - When a tenant needs a document signed, look up their specific template ID
   - Copy master templates into tenant organizations during provisioning

5. **Architecture pattern for multi-tenant platforms:**

   ```
   Your SaaS Platform
   ├── Tenant Management Service
   │   ├── Create/update/delete tenants
   │   ├── Tenant -> SignNow Org ID mapping
   │   └── Per-tenant configuration (branding, templates, settings)
   ├── Auth Broker
   │   ├── Per-tenant SignNow token management
   │   ├── Token caching with org-scoped keys
   │   └── Token refresh on expiration
   ├── Signing Service
   │   ├── Resolves tenant context from request
   │   ├── Uses tenant-scoped token for all API calls
   │   ├── Upload, invite, sign, download — all org-scoped
   │   └── Webhook routing to correct tenant handler
   └── Webhook Router
       ├── Receives SignNow webhooks
       ├── Maps document/invite IDs back to tenant
       └── Routes events to tenant-specific handlers
   ```

6. **Common multi-tenant scenarios:**

   - **Customer onboarding platform** — Each of your customers is a tenant; their end-users sign documents within the customer's branded experience
   - **Franchise management** — Each franchise location is a tenant with its own documents, signers, and branding
   - **HR SaaS** — Each company is a tenant; employees sign offer letters, policies, and forms within their company's workspace
   - **Legal practice management** — Each law firm is a tenant; clients sign engagement letters and agreements

7. **Security considerations:**
   - Never expose one tenant's documents or templates to another
   - Validate tenant context on every API call — do not rely on client-supplied tenant IDs without verification
   - Use separate webhook callback URLs or routing keys per tenant
   - Audit log per-tenant operations for compliance
   - Implement rate limiting per tenant to prevent one tenant from exhausting API quotas

8. **Reference documentation:**
   - [API Reference](https://docs.signnow.com/docs/signnow/reference)
   - [Developer Portal](https://www.signnow.com/developers)
   - [Branding Guide](https://docs.signnow.com/docs/signnow/guides-branding)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shadowrock-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
