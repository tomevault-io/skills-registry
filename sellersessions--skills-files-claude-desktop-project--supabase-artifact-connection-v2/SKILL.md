---
name: supabase-artifact-connection
description: Connect Supabase databases to Claude Desktop artifacts with authentication and read-only queries using native fetch API. Use when this capability is needed.
metadata:
  author: sellersessions
---

# Supabase Artifact Connection

## What's New (Updated Based on Engineering Feedback)

**CRITICAL UPDATE:** This skill now includes proper authentication flow.

**Previous Version Issue:**
- Only used URL + anon key
- Attempted to query data immediately
- Failed with RLS permission errors

**Current Version Fix:**
- Two-phase authentication: Project config (URL + anon key) + User auth (email/password)
- Login form automatically generated in artifacts
- Session management with persistence
- Works correctly with Row Level Security policies

**User Experience:**
1. User provides URL + anon key in prompt
2. Claude generates artifact with login form
3. User opens artifact, sees login screen
4. User enters Supabase Auth email/password at runtime
5. Artifact authenticates and displays data

## Core Principle

**Enable live database connections in Claude Desktop artifacts using Supabase REST API with native fetch(), proper authentication, and enforced read-only access.**

## Critical Issue: JS Client Library Doesn't Work in Artifacts

**PROBLEM:** The Supabase JavaScript client library (`@supabase/supabase-js@2`) causes `DataCloneError` in Claude Desktop artifacts due to iframe sandbox restrictions.

**SOLUTION:** Use Supabase REST API with native `fetch()` instead.

**DO NOT use this script:**
```html
<!-- ❌ NEVER USE - Causes DataCloneError in Claude Desktop -->
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
```

## When to Activate

This skill activates when:
- User mentions "Supabase" + "artifact" or "dashboard"
- User provides Supabase credentials (URL + anon key)
- User wants to query database data in an interactive artifact
- User asks to "connect to Supabase" or "load data from Supabase"

## Authentication Requirement

**CRITICAL:** Supabase queries require user authentication via email/password in addition to project configuration (URL + anon key).

### Two-Phase Connection Pattern

**Phase 1: Project Configuration** (what user provides in prompt)
- Supabase project URL (e.g., `https://xyz.supabase.co`)
- Anon key (starts with `eyJhbGc...`)

**Phase 2: User Authentication** (handled via login form in artifact)
- User email (Supabase Auth user)
- User password
- Session stored in browser after successful login

**Engineering Note:** You cannot query data with just URL + anon key. User must authenticate with `signInWithPassword()` first.

## Supabase REST API Wrapper

Always include this lightweight wrapper in artifacts - it mimics the Supabase client API but uses fetch():

```javascript
class SupabaseRestClient {
  constructor(url, key) {
    this.url = url;
    this.key = key;
    this.authToken = key; // Initially use anon key, will be replaced with session token
  }

  from(table) {
    return new QueryBuilder(this.url, this.authToken, table);
  }

  // Authentication methods
  get auth() {
    return {
      signInWithPassword: async ({ email, password }) => {
        try {
          const response = await fetch(`${this.url}/auth/v1/token?grant_type=password`, {
            method: 'POST',
            headers: {
              'apikey': this.key,
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({ email, password })
          });

          if (!response.ok) {
            const errorData = await response.json();
            return { data: null, error: errorData };
          }

          const data = await response.json();
          // Update auth token for subsequent queries
          this.authToken = data.access_token;
          return { data, error: null };
        } catch (error) {
          return { data: null, error: { message: error.message } };
        }
      },

      getSession: async () => {
        try {
          const response = await fetch(`${this.url}/auth/v1/user`, {
            headers: {
              'apikey': this.key,
              'Authorization': `Bearer ${this.authToken}`
            }
          });

          if (!response.ok) {
            return { data: { session: null }, error: null };
          }

          const user = await response.json();
          return { data: { session: { user } }, error: null };
        } catch (error) {
          return { data: { session: null }, error: null };
        }
      },

      signOut: async () => {
        this.authToken = this.key; // Reset to anon key
        return { error: null };
      }
    };
  }
}

class QueryBuilder {
  constructor(url, authToken, table) {
    this.url = url;
    this.authToken = authToken;
    this.table = table;
    this.selectCols = '*';
    this.filters = [];
    this.orderBy = null;
    this.limitCount = null;
  }

  select(columns = '*') {
    this.selectCols = columns;
    return this;
  }

  eq(column, value) {
    this.filters.push(`${column}=eq.${encodeURIComponent(value)}`);
    return this;
  }

  neq(column, value) {
    this.filters.push(`${column}=neq.${encodeURIComponent(value)}`);
    return this;
  }

  gt(column, value) {
    this.filters.push(`${column}=gt.${encodeURIComponent(value)}`);
    return this;
  }

  gte(column, value) {
    this.filters.push(`${column}=gte.${encodeURIComponent(value)}`);
    return this;
  }

  lt(column, value) {
    this.filters.push(`${column}=lt.${encodeURIComponent(value)}`);
    return this;
  }

  lte(column, value) {
    this.filters.push(`${column}=lte.${encodeURIComponent(value)}`);
    return this;
  }

  like(column, pattern) {
    this.filters.push(`${column}=like.${encodeURIComponent(pattern)}`);
    return this;
  }

  ilike(column, pattern) {
    this.filters.push(`${column}=ilike.${encodeURIComponent(pattern)}`);
    return this;
  }

  in(column, values) {
    this.filters.push(`${column}=in.(${values.map(v => encodeURIComponent(v)).join(',')})`);
    return this;
  }

  is(column, value) {
    this.filters.push(`${column}=is.${value === null ? 'null' : encodeURIComponent(value)}`);
    return this;
  }

  order(column, { ascending = true } = {}) {
    this.orderBy = `${column}.${ascending ? 'asc' : 'desc'}`;
    return this;
  }

  limit(count) {
    this.limitCount = count;
    return this;
  }

  async execute() {
    try {
      const params = [`select=${this.selectCols}`];
      if (this.filters.length > 0) params.push(...this.filters);
      if (this.orderBy) params.push(`order=${this.orderBy}`);
      if (this.limitCount) params.push(`limit=${this.limitCount}`);

      const url = `${this.url}/rest/v1/${this.table}?${params.join('&')}`;

      const response = await fetch(url, {
        headers: {
          'apikey': this.authToken,
          'Authorization': `Bearer ${this.authToken}`,
          'Content-Type': 'application/json'
        }
      });

      if (!response.ok) {
        const errorData = await response.json().catch(() => ({ message: response.statusText }));
        return { data: null, error: errorData };
      }

      const data = await response.json();
      return { data, error: null };

    } catch (error) {
      return { data: null, error: { message: error.message } };
    }
  }
}

function createSupabaseClient(url, key) {
  return new SupabaseRestClient(url, key);
}
```

## Standard Initialization Pattern

```javascript
// Initialize Supabase client (REST API wrapper)
const SUPABASE_URL = 'https://[project-id].supabase.co';
const SUPABASE_ANON_KEY = 'eyJhbGc...';

const supabase = createSupabaseClient(SUPABASE_URL, SUPABASE_ANON_KEY);

// Check if user is already authenticated
async function checkAuth() {
  const { data: { session } } = await supabase.auth.getSession();
  if (session) {
    showDashboard(); // User is logged in
  } else {
    showLoginForm(); // Need to login
  }
}

// Handle login
async function handleLogin(email, password) {
  const { data, error } = await supabase.auth.signInWithPassword({
    email: email,
    password: password
  });

  if (error) {
    console.error('Login error:', error);
    return false;
  }

  return true; // Login successful
}

// Query example (after authentication)
async function loadData() {
  const { data, error } = await supabase
    .from('table_name')
    .select('*')
    .limit(100)
    .execute();  // Note: .execute() is required with REST wrapper

  if (error) {
    console.error('Query error:', error);
    return null;
  }

  return data;
}

// Initialize on page load
window.addEventListener('DOMContentLoaded', checkAuth);
```

## Read-Only Access Rules

**CRITICAL: NEVER generate code that writes to Supabase.**

### ✅ ALLOWED Operations:
- `.select()` - Query data
- `.from()` - Specify table
- Filters: `.eq()`, `.neq()`, `.gt()`, `.gte()`, `.lt()`, `.lte()`, `.like()`, `.ilike()`, `.in()`, `.is()`
- Ordering: `.order()`
- Limits: `.limit()`

### ❌ FORBIDDEN Operations:
- `.insert()` - Create records
- `.update()` - Modify records
- `.upsert()` - Insert or update
- `.delete()` - Remove records
- Any mutation operations

**If user requests write operations, respond:**
> "This skill only supports read-only queries to protect database integrity. For write operations, use Supabase Dashboard or a dedicated backend service."

## Query Patterns

### Basic Query
```javascript
const { data, error } = await supabase
  .from('products')
  .select('*')
  .execute();
```

### Filtered Query
```javascript
const { data, error } = await supabase
  .from('products')
  .select('name, price, category')
  .eq('category', 'Electronics')
  .gt('price', 100)
  .order('price', { ascending: false })
  .limit(50)
  .execute();
```

### Search Query (Case-Insensitive)
```javascript
const { data, error } = await supabase
  .from('customers')
  .select('*')
  .ilike('name', `%${searchTerm}%`)
  .execute();
```

### Multiple Filters
```javascript
const { data, error } = await supabase
  .from('orders')
  .select('*')
  .eq('status', 'active')
  .gte('created_at', '2025-01-01')
  .lte('total', 1000)
  .limit(20)
  .execute();
```

### IN Filter
```javascript
const { data, error } = await supabase
  .from('products')
  .select('*')
  .in('category', ['Electronics', 'Books', 'Toys'])
  .execute();
```

## Complete Artifact Template

**Important:** Include the REST API wrapper classes AND authentication UI.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Supabase Data Viewer</title>

  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
      background: #f5f5f5;
      padding: 20px;
      line-height: 1.6;
    }

    .container {
      max-width: 1200px;
      margin: 0 auto;
      background: white;
      padding: 40px;
      border-radius: 12px;
      box-shadow: 0 2px 12px rgba(0,0,0,0.08);
    }

    h1 {
      color: #1a1a1a;
      margin-bottom: 10px;
      font-size: 28px;
    }

    .subtitle {
      color: #666;
      margin-bottom: 30px;
      font-size: 14px;
    }

    /* Login form styles */
    .login-container {
      max-width: 400px;
      margin: 60px auto;
    }

    .login-form {
      display: flex;
      flex-direction: column;
      gap: 15px;
    }

    .form-group {
      display: flex;
      flex-direction: column;
      gap: 5px;
    }

    .form-group label {
      font-weight: 500;
      font-size: 14px;
      color: #333;
    }

    .form-group input {
      padding: 10px;
      border: 1px solid #ddd;
      border-radius: 6px;
      font-size: 14px;
    }

    .form-group input:focus {
      outline: none;
      border-color: #3b82f6;
    }

    .login-button {
      padding: 12px;
      background: #3b82f6;
      color: white;
      border: none;
      border-radius: 6px;
      font-size: 14px;
      font-weight: 500;
      cursor: pointer;
      margin-top: 10px;
    }

    .login-button:hover {
      background: #2563eb;
    }

    .login-button:disabled {
      background: #9ca3af;
      cursor: not-allowed;
    }

    .logout-button {
      padding: 8px 16px;
      background: #dc2626;
      color: white;
      border: none;
      border-radius: 6px;
      font-size: 13px;
      cursor: pointer;
      margin-bottom: 20px;
    }

    .logout-button:hover {
      background: #b91c1c;
    }

    .hidden {
      display: none;
    }

    .loading {
      padding: 40px;
      text-align: center;
      color: #666;
    }

    .error {
      background: #ffebee;
      border: 1px solid #e57373;
      padding: 15px;
      border-radius: 6px;
      color: #c62828;
      margin: 20px 0;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
    }

    th, td {
      text-align: left;
      padding: 12px;
      border-bottom: 1px solid #ddd;
    }

    th {
      background: #f8f9fa;
      font-weight: 600;
      font-size: 13px;
    }

    tbody tr:hover {
      background: #fafafa;
    }
  </style>
</head>
<body>
  <!-- Login Screen -->
  <div id="loginScreen" class="hidden">
    <div class="login-container">
      <h1>Login to Supabase</h1>
      <div class="subtitle">Enter your credentials to access data</div>
      <form id="loginForm" class="login-form">
        <div class="form-group">
          <label for="email">Email</label>
          <input type="email" id="email" required placeholder="your@email.com">
        </div>
        <div class="form-group">
          <label for="password">Password</label>
          <input type="password" id="password" required placeholder="Your password">
        </div>
        <div id="loginError" class="error hidden"></div>
        <button type="submit" class="login-button">Login</button>
      </form>
    </div>
  </div>

  <!-- Dashboard (shown after login) -->
  <div id="dashboard" class="hidden">
    <div class="container">
      <button id="logoutButton" class="logout-button">Logout</button>
      <h1>Supabase Data</h1>
      <div class="subtitle">Connected to: [PROJECT_URL]</div>
      <div id="content">
        <div class="loading">Loading data...</div>
      </div>
    </div>
  </div>

  <script>
    // ============================================
    // SUPABASE REST API WRAPPER WITH AUTH (Required for Claude Desktop)
    // ============================================
    class SupabaseRestClient {
      constructor(url, key) {
        this.url = url;
        this.key = key;
        this.authToken = key;
      }

      from(table) {
        return new QueryBuilder(this.url, this.authToken, table);
      }

      get auth() {
        return {
          signInWithPassword: async ({ email, password }) => {
            try {
              const response = await fetch(`${this.url}/auth/v1/token?grant_type=password`, {
                method: 'POST',
                headers: {
                  'apikey': this.key,
                  'Content-Type': 'application/json'
                },
                body: JSON.stringify({ email, password })
              });

              if (!response.ok) {
                const errorData = await response.json();
                return { data: null, error: errorData };
              }

              const data = await response.json();
              this.authToken = data.access_token;
              return { data, error: null };
            } catch (error) {
              return { data: null, error: { message: error.message } };
            }
          },

          getSession: async () => {
            try {
              const response = await fetch(`${this.url}/auth/v1/user`, {
                headers: {
                  'apikey': this.key,
                  'Authorization': `Bearer ${this.authToken}`
                }
              });

              if (!response.ok) {
                return { data: { session: null }, error: null };
              }

              const user = await response.json();
              return { data: { session: { user } }, error: null };
            } catch (error) {
              return { data: { session: null }, error: null };
            }
          },

          signOut: async () => {
            this.authToken = this.key;
            return { error: null };
          }
        };
      }
    }

    class QueryBuilder {
      constructor(url, authToken, table) {
        this.url = url;
        this.authToken = authToken;
        this.table = table;
        this.selectCols = '*';
        this.filters = [];
        this.orderBy = null;
        this.limitCount = null;
      }
      select(columns = '*') { this.selectCols = columns; return this; }
      eq(column, value) { this.filters.push(`${column}=eq.${encodeURIComponent(value)}`); return this; }
      neq(column, value) { this.filters.push(`${column}=neq.${encodeURIComponent(value)}`); return this; }
      gt(column, value) { this.filters.push(`${column}=gt.${encodeURIComponent(value)}`); return this; }
      gte(column, value) { this.filters.push(`${column}=gte.${encodeURIComponent(value)}`); return this; }
      lt(column, value) { this.filters.push(`${column}=lt.${encodeURIComponent(value)}`); return this; }
      lte(column, value) { this.filters.push(`${column}=lte.${encodeURIComponent(value)}`); return this; }
      like(column, pattern) { this.filters.push(`${column}=like.${encodeURIComponent(pattern)}`); return this; }
      ilike(column, pattern) { this.filters.push(`${column}=ilike.${encodeURIComponent(pattern)}`); return this; }
      in(column, values) { this.filters.push(`${column}=in.(${values.map(v => encodeURIComponent(v)).join(',')})`); return this; }
      is(column, value) { this.filters.push(`${column}=is.${value === null ? 'null' : encodeURIComponent(value)}`); return this; }
      order(column, { ascending = true } = {}) { this.orderBy = `${column}.${ascending ? 'asc' : 'desc'}`; return this; }
      limit(count) { this.limitCount = count; return this; }

      async execute() {
        try {
          const params = [`select=${this.selectCols}`];
          if (this.filters.length > 0) params.push(...this.filters);
          if (this.orderBy) params.push(`order=${this.orderBy}`);
          if (this.limitCount) params.push(`limit=${this.limitCount}`);

          const url = `${this.url}/rest/v1/${this.table}?${params.join('&')}`;

          const response = await fetch(url, {
            headers: {
              'apikey': this.authToken,
              'Authorization': `Bearer ${this.authToken}`,
              'Content-Type': 'application/json'
            }
          });

          if (!response.ok) {
            const errorData = await response.json().catch(() => ({ message: response.statusText }));
            return { data: null, error: errorData };
          }

          const data = await response.json();
          return { data, error: null };
        } catch (error) {
          return { data: null, error: { message: error.message } };
        }
      }
    }

    function createSupabaseClient(url, key) {
      return new SupabaseRestClient(url, key);
    }

    // ============================================
    // CONFIGURATION
    // ============================================
    const SUPABASE_URL = 'https://[PROJECT_ID].supabase.co';
    const SUPABASE_ANON_KEY = 'eyJhbGc...';

    const supabase = createSupabaseClient(SUPABASE_URL, SUPABASE_ANON_KEY);

    // ============================================
    // UI ELEMENTS
    // ============================================
    const loginScreen = document.getElementById('loginScreen');
    const dashboard = document.getElementById('dashboard');
    const loginForm = document.getElementById('loginForm');
    const loginError = document.getElementById('loginError');
    const logoutButton = document.getElementById('logoutButton');

    // ============================================
    // AUTHENTICATION HANDLERS
    // ============================================
    async function checkAuth() {
      const { data: { session } } = await supabase.auth.getSession();
      if (session) {
        showDashboard();
      } else {
        showLogin();
      }
    }

    function showLogin() {
      loginScreen.classList.remove('hidden');
      dashboard.classList.add('hidden');
    }

    function showDashboard() {
      loginScreen.classList.add('hidden');
      dashboard.classList.remove('hidden');
      loadData();
    }

    loginForm.addEventListener('submit', async (e) => {
      e.preventDefault();
      loginError.classList.add('hidden');

      const email = document.getElementById('email').value;
      const password = document.getElementById('password').value;

      const { data, error } = await supabase.auth.signInWithPassword({
        email: email,
        password: password
      });

      if (error) {
        loginError.textContent = error.message || 'Login failed. Please check your credentials.';
        loginError.classList.remove('hidden');
      } else {
        showDashboard();
      }
    });

    logoutButton.addEventListener('click', async () => {
      await supabase.auth.signOut();
      showLogin();
    });

    // ============================================
    // LOAD AND DISPLAY DATA
    // ============================================
    async function loadData() {
      const contentDiv = document.getElementById('content');

      try {
        const { data, error } = await supabase
          .from('table_name')
          .select('*')
          .limit(100)
          .execute();

        if (error) throw new Error(error.message || JSON.stringify(error));

        if (!data || data.length === 0) {
          contentDiv.innerHTML = '<p>No data found.</p>';
          return;
        }

        // Create table
        const columns = Object.keys(data[0]);
        let html = '<table><thead><tr>';
        columns.forEach(col => html += `<th>${col}</th>`);
        html += '</tr></thead><tbody>';

        data.forEach(row => {
          html += '<tr>';
          columns.forEach(col => html += `<td>${row[col] ?? ''}</td>`);
          html += '</tr>';
        });

        html += '</tbody></table>';
        contentDiv.innerHTML = html;

      } catch (error) {
        console.error('Error:', error);
        contentDiv.innerHTML = `<div class="error">Error loading data: ${error.message}</div>`;
      }
    }

    // Initialize on page load
    window.addEventListener('DOMContentLoaded', checkAuth);
  </script>
</body>
</html>
```

## Error Handling

Always include comprehensive error handling:

```javascript
try {
  const { data, error } = await supabase
    .from('table_name')
    .select('*')
    .execute();

  if (error) {
    throw new Error(error.message || JSON.stringify(error));
  }

  // Process data
  console.log('Data loaded:', data);

} catch (error) {
  console.error('Supabase error:', error);
  // Show user-friendly error message
  document.getElementById('content').innerHTML =
    `<div class="error">Failed to load data: ${error.message}</div>`;
}
```

## Common Errors

**Error: "Invalid login credentials" or authentication failed**
- User email/password are incorrect
- User does not exist in Supabase Auth
- Check credentials in Supabase Dashboard → Authentication → Users

**Error: "Invalid API key" or 401**
- Check SUPABASE_ANON_KEY is correct
- Ensure key starts with `eyJhbGc...`
- Verify key matches project in Supabase Dashboard → Settings → API

**Error: "Table not found" or 404**
- Verify table name spelling
- Check table exists in Supabase Dashboard → Table Editor
- Ensure anon key has read permissions

**Error: "Row Level Security policy violation" or 403**
- Most common error: User authenticated but RLS policy blocks access
- Table has RLS enabled but no policy for authenticated users
- Fix: Add RLS policy in Supabase Dashboard → Authentication → Policies
- Example policy for authenticated read access:
  ```sql
  CREATE POLICY "Allow authenticated users to read"
  ON table_name
  FOR SELECT
  TO authenticated
  USING (true);
  ```

**Error: "CORS error"**
- Should not occur with official Supabase hosting
- If using self-hosted, check CORS configuration

**Error: Login form not showing**
- Check browser console for JavaScript errors
- Verify `.hidden` class is defined in CSS
- Ensure `checkAuth()` is called on page load

## Success Checklist

Before sharing artifact with user:
- ✅ REST API wrapper WITH AUTH included in artifact (NOT CDN script)
- ✅ Client initialized with user's project URL and anon key
- ✅ Login form implemented with email/password inputs
- ✅ Session checking on page load (`checkAuth()`)
- ✅ Logout button included
- ✅ Query uses read-only operations only
- ✅ `.execute()` called at end of query chain
- ✅ Error handling included (both auth and data errors)
- ✅ Loading state shown to user
- ✅ Data displayed in readable format

**Important User Instructions:**
When artifact is opened, user needs to:
1. Enter their Supabase Auth email (created in Supabase Dashboard)
2. Enter their password
3. Login will authenticate and display data
4. Session persists on refresh (no re-login needed until logout)

## Example Use Cases

**Simple Data Table:**
```javascript
const { data, error } = await supabase
  .from('customers')
  .select('*')
  .limit(50)
  .execute();
```

**Filtered Dashboard:**
```javascript
const { data, error } = await supabase
  .from('users')
  .select('name, email, created_at')
  .eq('status', 'active')
  .order('created_at', { ascending: false })
  .execute();
```

**Search Interface:**
```javascript
const { data, error } = await supabase
  .from('products')
  .select('*')
  .ilike('name', `%${searchTerm}%`)
  .execute();
```

## Next Steps After Connection

Once basic connection works:
1. Add interactive filters (dropdowns, search)
2. Implement data visualization (charts, graphs)
3. Add export functionality (CSV, JSON)
4. Create multi-table views
5. Build custom UI components

**Remember:** Always start with basic connection, then enhance incrementally based on user needs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sellersessions) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
