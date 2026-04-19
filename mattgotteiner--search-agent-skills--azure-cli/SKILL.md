---
name: azure-cli
description: Execute Azure CLI (az) commands to manage Azure resources, get access tokens, and interact with Azure Search services. Use this skill when you need to authenticate with Azure, get bearer tokens for Azure services, manage Azure Search indexes/indexers/datasources, or perform any Azure resource management tasks. Use when this capability is needed.
metadata:
  author: mattgotteiner
---

# Azure CLI Skill

This skill enables you to execute Azure CLI (`az`) commands to manage Azure resources and services. It is particularly useful for Azure Search operations and authentication scenarios.

## Prerequisites

- Azure CLI installed (`az` command available in PATH)
- Logged in to Azure (`az login` completed)
- Appropriate Azure subscription and permissions

## Authentication

### Check Login Status

```bash
az account show
```

### Login (if not already logged in)

```bash
az login
```

### Login with Device Code (for environments without browser)

```bash
az login --use-device-code
```

### Set Active Subscription

```bash
az account set --subscription "YOUR_SUBSCRIPTION_NAME_OR_ID"
```

## Getting Access Tokens

Use `az account get-access-token` to obtain bearer tokens for authenticating with Azure services.

### Get Token for Azure Search

```bash
az account get-access-token --resource https://search.azure.com --query accessToken --output tsv
```

### Get Token with Full Details

```bash
az account get-access-token --resource https://search.azure.com
```

Output includes:
- `accessToken`: The bearer token
- `expiresOn`: Token expiration time
- `subscription`: Azure subscription ID
- `tenant`: Azure AD tenant ID

### Get Token for Other Azure Resources

```bash
# Azure Resource Manager
az account get-access-token --resource https://management.azure.com

# Azure Key Vault
az account get-access-token --resource https://vault.azure.net

# Microsoft Graph
az account get-access-token --resource https://graph.microsoft.com

# Azure Storage
az account get-access-token --resource https://storage.azure.com
```

### Using the Token in HTTP Requests

Once you have the token, use it as a Bearer token in the Authorization header. Use the [rest-http-caller skill](../rest-http-caller/SKILL.md) to make HTTP requests:

```bash
# Get token and store in variable
$TOKEN = az account get-access-token --resource https://search.azure.com --query accessToken --output tsv

# Use in HTTP request with the HttpCaller skill
dotnet run ../rest-http-caller/HttpCaller.cs -- GET "https://your-search-service.search.windows.net/indexes?api-version=2024-07-01" --header "Authorization: Bearer $TOKEN"
```

## Azure Search CLI Commands

Azure Search (Azure AI Search) commands are available under `az search`. These commands help manage search services, indexes, and related resources.

### Service Management

#### List Search Services

```bash
# List all search services in a resource group
az search service list --resource-group YOUR_RESOURCE_GROUP

# List all search services in the subscription
az search service list
```

#### Show Search Service Details

```bash
az search service show --name YOUR_SEARCH_SERVICE --resource-group YOUR_RESOURCE_GROUP
```

#### Create a Search Service

```bash
az search service create \
  --name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --location eastus \
  --sku standard
```

Available SKUs: `free`, `basic`, `standard`, `standard2`, `standard3`, `storage_optimized_l1`, `storage_optimized_l2`

#### Delete a Search Service

```bash
az search service delete \
  --name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --yes
```

#### Update a Search Service

```bash
az search service update \
  --name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --replica-count 2 \
  --partition-count 2
```

#### Check Name Availability

```bash
az search service check-name-availability --name PROPOSED_SERVICE_NAME
```

### Admin Key Management

Admin keys provide full access to the search service.

#### List Admin Keys

```bash
az search admin-key show \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP
```

Or using the service subcommand:

```bash
az search service admin-key list \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP
```

#### Regenerate Admin Key

```bash
# Regenerate primary key
az search admin-key renew \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --key-kind primary

# Regenerate secondary key
az search admin-key renew \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --key-kind secondary
```

### Query Key Management

Query keys provide read-only access for querying indexes.

#### List Query Keys

```bash
az search query-key list \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP
```

#### Create a Query Key

```bash
az search query-key create \
  --name MY_QUERY_KEY \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP
```

#### Delete a Query Key

```bash
az search query-key delete \
  --key-value YOUR_KEY_VALUE \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --yes
```

### Private Endpoint Management

Manage private endpoint connections for secure access.

#### List Private Endpoint Connections

```bash
az search private-endpoint-connection list \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP
```

#### Show Private Endpoint Connection

```bash
az search private-endpoint-connection show \
  --name CONNECTION_NAME \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP
```

#### Update Private Endpoint Connection

```bash
az search private-endpoint-connection update \
  --name CONNECTION_NAME \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --status Approved \
  --description "Approved by admin"
```

#### Delete Private Endpoint Connection

```bash
az search private-endpoint-connection delete \
  --name CONNECTION_NAME \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --yes
```

### Shared Private Link Resources

Manage outbound connections from your search service to other Azure resources.

#### List Shared Private Link Resources

```bash
az search shared-private-link-resource list \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP
```

#### Create Shared Private Link Resource

```bash
az search shared-private-link-resource create \
  --name MY_SHARED_LINK \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --group-id blob \
  --resource-id /subscriptions/SUB_ID/resourceGroups/RG/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT
```

### Private Link Resources

List supported private link resource types.

```bash
az search private-link-resource list \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP
```

## Common Workflows

### Workflow 1: Get Bearer Token and Query Search Index

Use this skill together with the [rest-http-caller skill](../rest-http-caller/SKILL.md) to query Azure Search:

```bash
# 1. Get access token for Azure Search
$TOKEN = az account get-access-token --resource https://search.azure.com --query accessToken --output tsv

# 2. List indexes (using the rest-http-caller skill)
dotnet run ../rest-http-caller/HttpCaller.cs -- GET "https://YOUR_SEARCH_SERVICE.search.windows.net/indexes?api-version=2024-07-01" --header "Authorization: Bearer $TOKEN"

# 3. Query an index
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SEARCH_SERVICE.search.windows.net/indexes/YOUR_INDEX/docs/search?api-version=2024-07-01" --header "Authorization: Bearer $TOKEN" --header "Content-Type: application/json" --body '{"search": "*", "top": 10}'
```

### Workflow 2: Create and Configure a Search Service

```bash
# 1. Check name availability
az search service check-name-availability --name my-new-search

# 2. Create the service
az search service create \
  --name my-new-search \
  --resource-group my-resource-group \
  --location eastus \
  --sku standard

# 3. Get admin keys
az search admin-key show \
  --service-name my-new-search \
  --resource-group my-resource-group

# 4. Create a query key for applications
az search query-key create \
  --name app-query-key \
  --service-name my-new-search \
  --resource-group my-resource-group
```

### Workflow 3: Manage Keys for Security Rotation

```bash
# 1. List current query keys
az search query-key list \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP

# 2. Create a new query key
az search query-key create \
  --name new-app-key \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP

# 3. (After updating apps) Delete old query key
az search query-key delete \
  --key-value OLD_KEY_VALUE \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --yes

# 4. Regenerate admin keys if needed
az search admin-key renew \
  --service-name YOUR_SEARCH_SERVICE \
  --resource-group YOUR_RESOURCE_GROUP \
  --key-kind primary
```

## Output Formats

Azure CLI supports multiple output formats. Use `--output` or `-o` to specify:

| Format | Description |
|--------|-------------|
| `json` | JSON format (default) |
| `jsonc` | Colorized JSON |
| `table` | ASCII table format |
| `tsv` | Tab-separated values (good for scripts) |
| `yaml` | YAML format |
| `none` | No output |

### Examples

```bash
# Get token as plain text (for use in scripts)
az account get-access-token --resource https://search.azure.com --query accessToken --output tsv

# Get service list as table
az search service list --resource-group my-rg --output table

# Get admin keys as JSON
az search admin-key show --service-name my-search --resource-group my-rg --output json
```

## Using JMESPath Queries

Use `--query` to extract specific fields from the output:

```bash
# Get only the access token
az account get-access-token --resource https://search.azure.com --query accessToken -o tsv

# Get service endpoint
az search service show --name my-search --resource-group my-rg --query "endpoint" -o tsv

# Get list of service names
az search service list --resource-group my-rg --query "[].name" -o tsv
```

## Error Handling

Common errors and solutions:

| Error | Solution |
|-------|----------|
| "Please run 'az login'" | Run `az login` to authenticate |
| "Subscription not found" | Run `az account set --subscription NAME` |
| "Resource group not found" | Verify the resource group name exists |
| "ResourceNotFound" | Check the resource name and permissions |

## Tips for LLM Usage

1. **Always verify login status** before running commands: `az account show`
2. **Store tokens in variables** for reuse in scripts
3. **Use TSV output** when capturing values for use in other commands
4. **Use `--query`** to extract specific fields from complex outputs
5. **Replace placeholders**: `YOUR_SEARCH_SERVICE`, `YOUR_RESOURCE_GROUP`, etc.

## Reference Links

- [Azure CLI Documentation](https://learn.microsoft.com/en-us/cli/azure/)
- [az search commands](https://learn.microsoft.com/en-us/cli/azure/search)
- [az account get-access-token](https://learn.microsoft.com/en-us/cli/azure/account#az-account-get-access-token)
- [Azure AI Search Documentation](https://learn.microsoft.com/en-us/azure/search/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mattgotteiner) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
