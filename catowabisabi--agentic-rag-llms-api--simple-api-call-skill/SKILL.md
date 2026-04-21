---
name: simple-api-call-skill
description: 用來在功能開發前，模擬新手用戶行為，找 UX 與流程問題。 Use when this capability is needed.
metadata:
  author: catowabisabi
---

# Simple API Call Testing Skill

Automate API testing for NodeBB and Life Services endpoints with structured documentation and response logging.

## Workflow Integration

**Before starting any new test creation:**
1.  **Check Existing Code**: Use the `review-existing-code-references-skill` to check `existing-code-for-reference.md`. Look for existing API tests or scripts that can be reused or adapted.
2.  **Update References**: If you create a new reusable test script, use the `review-existing-code-references-skill` to add it to the reference file.

## Overview

This skill provides tools to systematically test REST APIs with proper logging, response tracking, and organized file storage. Perfect for validating API functionality, performance testing, and debugging.

## Core Features

- **Automated API Testing**: Generate Python test scripts for any API endpoint
- **Response Logging**: Structured JSON logging of all API responses
- **Organized Storage**: Time-stamped files with clear directory structure
- **Multi-API Support**: Works with NodeBB Read/Write APIs and Life Services

## API References

### NodeBB Read API
- **Base URL**: `https://nb-bbs.hk-garden.com/api/`
- **Authentication**: None required for public content
- **Key Endpoints**:
  - `/categories` - Get forum categories (✅ Tested: 15 main, 46 subcategories)
  - `/recent` - Recent topics
  - `/topic/{tid}` - Topic details with posts
  - `/user/{uid}` - User profile data

### NodeBB Write API  
- **Base URL**: `https://nb-bbs.hk-garden.com/api/v3/`
- **Authentication**: Session-based with CSRF token
- **Key Endpoints**:
  - `/topics` - Create/modify topics (✅ Tested: Successfully created topic 13452)
  - `/posts` - Create/modify posts
  - `/users` - User management
  - `/categories` - Category management

### Category References (from successful API test)
- **移民台** (Immigration): CID 18
  - **接機台** (Pickup Service): CID 78 (✅ Successfully tested post creation)
  - **房屋台** (Housing): CID 19
  - **工作台** (Jobs): CID 20

### Life Services API
- **Base URL**: `https://nb-bbs.hk-garden.com/api-proxy/api/life-services/`
- **Authentication**: Session-based for protected endpoints
- **Key Endpoints**:
  - `/housing` - Housing listings
  - `/jobs` - Job postings and applications
  - `/services` - Service listings with reviews
  - `/doctors` - Doctor profiles and appointments

## Usage Instructions

When user requests API testing, follow this workflow:

1. **Identify API Function**: Determine which API endpoint to test
2. **Generate Test Script**: Create Python script with proper error handling
3. **Execute and Log**: Run the test and capture response data
4. **Save Results**: Store both script and response in organized directories

### File Structure

```
test/api-test/api_call/
├── [function_name]/
│   ├── test_script/
│   │   └── YYYY-MM-DD-HH-mm-ss-[name].py
│   └── response/
│       └── YYYY-MM-DD-HH-mm-ss-[name].json
```

### Response JSON Format

```json
{
  "functions_desc": "Description of API function being tested",
  "api_url": "Full API endpoint URL",
  "response_status": 200,
  "time_stamp": "2026-01-03T14:30:45.123Z",
  "response_body": { ... }
}
```

## Python Script Template

```python
import requests
import json
from datetime import datetime
import os

def test_api_call():
    # API Configuration
    api_url = "ENDPOINT_URL"
    headers = {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer YOUR_TOKEN'  # if needed
    }
    
    # Function description
    function_desc = "FUNCTION_DESCRIPTION"
    
    try:
        # Make API call
        response = requests.get(api_url, headers=headers, timeout=30)
        
        # Prepare response data
        response_data = {
            "functions_desc": function_desc,
            "api_url": api_url,
            "response_status": response.status_code,
            "time_stamp": datetime.utcnow().isoformat() + 'Z',
            "response_body": response.json() if response.headers.get('content-type', '').startswith('application/json') else response.text
        }
        
        # Save response
        timestamp = datetime.now().strftime("%Y-%m-%d-%H-%M-%S")
        filename = f"{timestamp}-{function_desc.replace(' ', '_').lower()}.json"
        
        os.makedirs("response", exist_ok=True)
        with open(f"response/{filename}", 'w', encoding='utf-8') as f:
            json.dump(response_data, f, indent=2, ensure_ascii=False)
        
        print(f"✅ API call successful - Status: {response.status_code}")
        print(f"📁 Response saved to: response/{filename}")
        
        return response_data
        
    except requests.exceptions.RequestException as e:
        error_data = {
            "functions_desc": function_desc,
            "api_url": api_url,
            "response_status": 0,
            "time_stamp": datetime.utcnow().isoformat() + 'Z',
            "response_body": {"error": str(e)}
        }
        
        timestamp = datetime.now().strftime("%Y-%m-%d-%H-%M-%S")
        filename = f"{timestamp}-{function_desc.replace(' ', '_').lower()}_ERROR.json"
        
        os.makedirs("response", exist_ok=True)
        with open(f"response/{filename}", 'w', encoding='utf-8') as f:
            json.dump(error_data, f, indent=2, ensure_ascii=False)
        
        print(f"❌ API call failed: {e}")
        print(f"📁 Error logged to: response/{filename}")
        
        return error_data

if __name__ == "__main__":
    test_api_call()
```

## Common Test Scenarios

### 1. Test Recent Topics API
```python
api_url = "https://nb-bbs.hk-garden.com/api-proxy/api/recent?page=1"
function_desc = "Get recent forum topics"
```

### 2. Test Life Services Housing
```python
api_url = "https://nb-bbs.hk-garden.com/api-proxy/api/life-services/housing"
function_desc = "List available housing"
```

### 3. Test Category List
```python
api_url = "https://nb-bbs.hk-garden.com/api-proxy/api/categories"
function_desc = "Get forum categories"
```

## Best Practices

- **Always include timeout**: Prevent hanging requests
- **Handle errors gracefully**: Log failures for debugging
- **Use descriptive names**: Make files easily identifiable
- **Include timestamps**: Track when tests were performed
- **Validate responses**: Check status codes and data structure
- **Organize by function**: Keep related tests grouped together

## Working NodeBB Authentication Pattern (TESTED ✅)

For NodeBB write operations, use this proven authentication flow:

```python
import requests

def login_and_get_csrf():
    """Complete working login and CSRF token flow"""
    session = requests.Session()
    
    # Step 1: Login with hardcoded CSRF token
    login_url = "https://nb-bbs.hk-garden.com/login"
    login_data = {"username": "demo2", "password": "demo123"}
    login_headers = {"Content-Type": "application/json", "x-csrf-token": "false"}
    
    response = session.post(login_url, json=login_data, headers=login_headers)
    
    # Step 2: Verify login success
    if response.status_code == 200 and response.json().get('next') == '/':
        print("✅ Login successful")
        
        # Step 3: Get real CSRF token after authentication
        config_response = session.get("https://nb-bbs.hk-garden.com/api/config")
        csrf_token = config_response.json().get('csrf_token')
        
        return session, csrf_token
    else:
        raise Exception(f"Login failed: {response.status_code}")

# Example: Create topic in pickup service category
def create_pickup_post():
    session, csrf_token = login_and_get_csrf()
    
    post_data = {
        'cid': 78,  # 接機台 (Pickup Service)
        'title': 'Test Pickup Service',
        'content': 'Service details here...',
        'tags': []
    }
    
    headers = {
        'Content-Type': 'application/json',
        'x-csrf-token': csrf_token
    }
    
    response = session.post(
        "https://nb-bbs.hk-garden.com/api/v3/topics",
        json=post_data,
        headers=headers
    )
    
    if response.status_code == 200:
        result = response.json()
        topic_id = result['response']['tid']
        return f"✅ Created topic {topic_id}"
    else:
        raise Exception(f"Failed to create post: {response.status_code}")
```

### Critical Success Factors
1. **Use `"false"` as string for initial login CSRF token**
2. **Only get real CSRF token AFTER successful login**
3. **Use JSON for all requests (login and API calls)**
4. **Check for `{"next": "/"}` response to confirm login**
5. **Session cookies handled automatically by requests.Session()**

### Response Format for Topic Creation
```json
{
  "status": {"code": "ok", "message": "OK"},
  "response": {
    "tid": 13452,
    "mainPid": 836,
    "slug": "13452/topic-title",
    "cid": 78,
    "title": "Topic Title",
    "uid": 1000012,
    "timestamp": 1767498015447
  }
}
```

## Authentication Notes

- **Read API**: Most endpoints work without authentication
- **Write API**: Requires session-based authentication with CSRF token
- **Life Services**: Mixed - some public, some require login
- **CORS Proxy**: Not needed for direct API access

### Test Account Credentials

**Regular User Account (✅ TESTED):**
- Username: `demo2`
- Password: `demo123`
- Successfully tested: Login + Post Creation

**Admin User Account:**
- Username: `demo3` 
- Password: `demo123`

*Note: These are test accounts for development/testing purposes only*

## Forum Categories Reference

Key category IDs for testing:
- **移民台** (Immigration): ID 18
  - **接機台** (Pickup Service): ID 78 (✅ Successfully created topic 13452)
  - **接機台** (Pickup Service): ID 78
  - **FAQ 移民常見問題**: ID 62
  - **生活資訊**: ID 55
- **房屋台** (Housing): ID 74
- **投資台** (Investment): ID 79
- **返工台** (Jobs): ID 26

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/catowabisabi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
