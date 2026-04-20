---
name: servicedesk-plus-helper
description: | Use when this capability is needed.
metadata:
  author: hassaanch
---

# ServiceDesk Plus Helper

Comprehensive automation and customization assistance for ManageEngine ServiceDesk Plus (SDP) including Deluge scripting, custom functions, form rules, and API integrations.

## What This Skill Does

- Write custom functions for Request, Change, and Problem modules
- Configure field and form rules for templates
- Create Deluge scripts for automation workflows
- Set up REST API integrations with external systems
- Design business rules for ticket automation
- Debug and troubleshoot existing SDP customizations

## What This Skill Does NOT Do

- Access or modify your actual SDP instance
- Manage SDP licenses or subscriptions
- Configure SSO or authentication settings
- Provide official ManageEngine support
- Handle database-level operations directly

---

## Before Implementation

Gather context to ensure successful implementation:

| Source | Gather |
|--------|--------|
| **SDP Environment** | Cloud vs On-Premises, version, enabled modules |
| **Conversation** | Specific requirements, trigger events, target fields |
| **Skill References** | Deluge patterns, API examples, rule configurations |
| **User Guidelines** | Naming conventions, error handling preferences |

---

## Required Clarifications

Ask about USER'S context before generating:

1. **Environment**: "Are you using ServiceDesk Plus Cloud or On-Premises?"
2. **Module**: "Which module is this for? (Request, Change, Problem, Asset, etc.)"
3. **Trigger**: "What event should trigger this? (On Create, On Edit, On Status Change, etc.)"

## Optional Clarifications

Ask only if relevant to the user's context:

1. **Integration**: "Do you need to call external APIs or only work within SDP?"
2. **Existing Setup**: "Do you have existing custom functions or rules that might conflict?"
3. **Notifications**: "Should this trigger any email or webhook notifications?"
4. **Permissions**: "Are there specific user roles that should be affected?"

---

## Official Documentation

| Resource | URL | Use For |
|----------|-----|---------|
| Custom Functions | https://help.sdpondemand.com/custom-functions | Custom function setup and triggers |
| Deluge Reference | https://www.zoho.com/deluge/help/ | Deluge language syntax |
| SDP REST API | https://www.manageengine.com/products/service-desk/sdpod-v3-api/ | API endpoints and authentication |
| Form Rules | https://help.sdpondemand.com/form-rules | Form-level automation |

---

## Cloud vs On-Premises Differences

| Feature | Cloud | On-Premises |
|---------|-------|-------------|
| **API Base URL** | `https://<instance>.sdpondemand.com/api/v3` | `https://<server>/api/v3` |
| **Authentication** | OAuth 2.0, API Key | API Key, Technician Key |
| **Custom Functions** | Admin > Automation > Custom Functions | Admin > Custom Functions |
| **Deluge Connections** | Built-in connection manager | Manual URL configuration |
| **Webhooks** | Native webhook support | Requires manual setup |
| **Rate Limits** | API rate limits apply | No external limits |
| **Updates** | Automatic | Manual upgrade required |

### Cloud-Specific Patterns

```javascript
// Cloud: Use built-in connections
response = zoho.sdp.getRecordById("requests", entityId);
```

### On-Premises Patterns

```javascript
// On-Premises: Use invokeurl with API key
headers = Map();
headers.put("authtoken", input.api_key);

response = invokeurl
[
    url: "https://your-server/api/v3/requests/" + entityId
    type: GET
    headers: headers
];
```

---

## Workflow

### Phase 1: Requirement Analysis

```
1. Identify the SDP environment (Cloud/On-Premises)
2. Determine the module (Request, Change, Problem)
3. Understand the trigger event
4. Map input fields to output actions
5. Identify any external system dependencies
```

### Phase 2: Solution Design

```
1. Choose solution type:
   - Custom Function (for complex logic)
   - Field Rule (for simple show/hide/mandatory)
   - Form Rule (for template-level behavior)
   - Business Rule (for automated actions)
2. Design the data flow
3. Plan error handling
4. Consider edge cases (null values, permissions)
```

### Phase 3: Implementation

```
1. Write Deluge script (if custom function)
2. Configure rules (if field/form rule)
3. Set up API connections (if integration)
4. Add logging for debugging
5. Document assumptions and dependencies
```

**See reference files for specific patterns.**

### Phase 4: Testing Guidance

```
1. Provide test scenarios to user
2. List expected behaviors
3. Suggest edge cases to verify
4. Recommend permission checks
```

---

## Module Reference

### Request Module
- Trigger events: On Create, On Edit, On Status Change, On Approval, On Assignment
- Key fields: subject, description, requester, category, status, priority, technician

### Change Module
- Trigger events: On Create, On Edit, On Stage Change, On Approval
- Key fields: title, change_type, impact, urgency, CAB_members

### Problem Module
- Trigger events: On Create, On Edit, On Status Change, On Resolution
- Key fields: title, symptoms, root_cause, workaround, related_requests

---

## Common Use Cases

| Use Case | Solution Type | Reference |
|----------|---------------|-----------|
| Auto-assign based on category | Custom Function | `references/custom-functions.md` |
| Show fields conditionally | Field Rule | `references/field-form-rules.md` |
| Sync with external CMDB | API Integration | `references/api-integration.md` |
| Send Slack notification | Deluge + Webhook | `references/deluge-scripting.md` |
| Make field mandatory on status | Form Rule | `references/field-form-rules.md` |
| Create linked tickets | Custom Function | `references/custom-functions.md` |

---

## Output Checklist

Before delivering, verify:

- [ ] Environment compatibility confirmed (Cloud/On-Premises)
- [ ] Module and trigger event correctly identified
- [ ] Deluge script includes error handling
- [ ] API calls include timeout and retry logic
- [ ] All field names match SDP naming conventions
- [ ] Code includes inline comments
- [ ] Test scenarios provided
- [ ] Edge cases documented

---

## Reference Files

| File | When to Read |
|------|--------------|
| `references/custom-functions.md` | When writing custom functions with Deluge |
| `references/field-form-rules.md` | When configuring field visibility or mandatory rules |
| `references/api-integration.md` | When integrating with REST APIs |
| `references/deluge-scripting.md` | For Deluge syntax and common patterns |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hassaanch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
