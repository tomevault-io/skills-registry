---
name: supabase-setup
description: Use when setting up Supabase, creating database tables, writing RLS policies, configuring Auth (especially Apple Sign-In), creating storage buckets, writing Edge Functions in TypeScript/Deno, or running migrations. Triggers on "Supabase setup", "RLS policy", "Edge Function", "database schema", "storage bucket", "Apple Sign-In auth".
metadata:
  author: bgrober
---

# Supabase Setup

Guide for configuring Supabase backend with proper security, RLS policies, and Edge Functions.

## When to Use

- Starting a new project with Supabase backend
- Adding new tables or features to existing Supabase project
- Setting up authentication (especially Apple Sign-In)
- Creating Edge Functions for AI or external APIs
- Reviewing security policies

## Project Structure

```
supabase/
├── config.toml              # Local dev config
├── functions/
│   └── function-name/       # Edge Function
│       ├── index.ts         # Request handling
│       └── helpers.ts       # Utilities
└── migrations/
    ├── 001_initial.sql      # Initial tables + RLS
    ├── 002_storage.sql      # Storage buckets + policies
    └── 003_new_feature.sql  # Incremental changes
```

## Database Setup

### 1. Table Design Pattern

```sql
-- Standard table structure
CREATE TABLE items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),

    -- Your columns
    title TEXT NOT NULL,
    status TEXT NOT NULL DEFAULT 'pending',
    score INTEGER,

    -- Constraints
    CONSTRAINT valid_status CHECK (status IN ('pending', 'success', 'failed'))
);

-- Auto-update updated_at
CREATE TRIGGER update_items_updated_at
    BEFORE UPDATE ON items
    FOR EACH ROW
    EXECUTE FUNCTION moddatetime(updated_at);

-- Index for user queries
CREATE INDEX items_user_id_idx ON items(user_id);
CREATE INDEX items_created_at_idx ON items(created_at DESC);
```

### 2. RLS Policies (CRITICAL)

```sql
-- Enable RLS (required!)
ALTER TABLE items ENABLE ROW LEVEL SECURITY;

-- Users can only see their own items
CREATE POLICY "Users can view own items"
    ON items FOR SELECT
    USING (auth.uid() = user_id);

-- Users can insert their own items
CREATE POLICY "Users can insert own items"
    ON items FOR INSERT
    WITH CHECK (auth.uid() = user_id);

-- Users can update their own items
CREATE POLICY "Users can update own items"
    ON items FOR UPDATE
    USING (auth.uid() = user_id)
    WITH CHECK (auth.uid() = user_id);

-- Users can delete their own items
CREATE POLICY "Users can delete own items"
    ON items FOR DELETE
    USING (auth.uid() = user_id);
```

### 3. Profiles Table (Auto-created on signup)

```sql
-- Profiles table
CREATE TABLE profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    email TEXT,
    display_name TEXT,
    avatar_url TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Users can view and update their own profile
CREATE POLICY "Users can view own profile"
    ON profiles FOR SELECT
    USING (auth.uid() = id);

CREATE POLICY "Users can update own profile"
    ON profiles FOR UPDATE
    USING (auth.uid() = id);

-- Auto-create profile on signup
CREATE OR REPLACE FUNCTION handle_new_user()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO profiles (id, email)
    VALUES (NEW.id, NEW.email);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_auth_user_created
    AFTER INSERT ON auth.users
    FOR EACH ROW
    EXECUTE FUNCTION handle_new_user();
```

## Storage Setup

### 1. Create Bucket

```sql
-- Create storage bucket
INSERT INTO storage.buckets (id, name, public)
VALUES ('item-images', 'item-images', false);
```

### 2. Storage Policies

```sql
-- Users can upload to their own folder
CREATE POLICY "Users can upload own images"
    ON storage.objects FOR INSERT
    WITH CHECK (
        bucket_id = 'item-images' AND
        auth.uid()::text = (storage.foldername(name))[1]
    );

-- Users can view their own images
CREATE POLICY "Users can view own images"
    ON storage.objects FOR SELECT
    USING (
        bucket_id = 'item-images' AND
        auth.uid()::text = (storage.foldername(name))[1]
    );

-- Users can delete their own images
CREATE POLICY "Users can delete own images"
    ON storage.objects FOR DELETE
    USING (
        bucket_id = 'item-images' AND
        auth.uid()::text = (storage.foldername(name))[1]
    );
```

### 3. File Path Convention

```
{bucket}/{user_id}/{item_id}.{ext}
```

Example: `item-images/abc123/def456.jpg`

## Authentication

### Apple Sign-In Setup

1. **Supabase Dashboard:**
   - Auth → Providers → Apple
   - Enable Apple provider
   - Add Service ID and Team ID

2. **config.toml (local dev):**
```toml
[auth.external.apple]
enabled = true
client_id = "your.service.id"
```

3. **iOS App:**
```swift
// Use Supabase Swift SDK
let credentials = try await supabase.auth.signInWithApple()
```

### Session Handling

- Access tokens expire (default: 1 hour)
- Refresh tokens stored in Keychain
- SDK handles refresh automatically
- Handle 401 errors with manual refresh fallback

## Edge Functions

### 1. Basic Structure

```typescript
// supabase/functions/process-item/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts"
import { createClient } from "https://esm.sh/@supabase/supabase-js@2"

const corsHeaders = {
  'Access-Control-Allow-Origin': '*',
  'Access-Control-Allow-Headers': 'authorization, x-client-info, apikey, content-type',
}

serve(async (req) => {
  // Handle CORS preflight
  if (req.method === 'OPTIONS') {
    return new Response('ok', { headers: corsHeaders })
  }

  try {
    // Verify auth
    const authHeader = req.headers.get('Authorization')
    if (!authHeader) {
      return new Response(
        JSON.stringify({ error: 'Missing authorization' }),
        { status: 401, headers: { ...corsHeaders, 'Content-Type': 'application/json' } }
      )
    }

    // Create authenticated client
    const supabase = createClient(
      Deno.env.get('SUPABASE_URL') ?? '',
      Deno.env.get('SUPABASE_ANON_KEY') ?? '',
      { global: { headers: { Authorization: authHeader } } }
    )

    // Verify user
    const { data: { user }, error: userError } = await supabase.auth.getUser()
    if (userError || !user) {
      return new Response(
        JSON.stringify({ error: 'Invalid token' }),
        { status: 401, headers: { ...corsHeaders, 'Content-Type': 'application/json' } }
      )
    }

    // Parse request
    const { itemId, data } = await req.json()

    // Your logic here
    const result = await processItem(itemId, data)

    return new Response(
      JSON.stringify(result),
      { headers: { ...corsHeaders, 'Content-Type': 'application/json' } }
    )

  } catch (error) {
    console.error('Error:', error)
    return new Response(
      JSON.stringify({ error: 'Internal server error' }),
      { status: 500, headers: { ...corsHeaders, 'Content-Type': 'application/json' } }
    )
  }
})
```

### 2. Calling External APIs (e.g., Gemini)

```typescript
// Get API key from environment
const GEMINI_API_KEY = Deno.env.get('GEMINI_API_KEY')

async function callGemini(prompt: string, imageBase64: string) {
  const response = await fetch(
    `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${GEMINI_API_KEY}`,
    {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        contents: [{
          parts: [
            { text: prompt },
            { inline_data: { mime_type: 'image/jpeg', data: imageBase64 } }
          ]
        }],
        generationConfig: {
          responseMimeType: 'application/json'
        }
      })
    }
  )

  if (!response.ok) {
    throw new Error(`Gemini API error: ${response.status}`)
  }

  const result = await response.json()
  return JSON.parse(result.candidates[0].content.parts[0].text)
}
```

### 3. Setting Secrets

```bash
# Set secret in Supabase
supabase secrets set GEMINI_API_KEY=your-key-here

# List secrets
supabase secrets list
```

## Migration Workflow

### Creating Migrations

```bash
# Create new migration
supabase migration new add_feature_x

# Edit the generated file
# supabase/migrations/YYYYMMDDHHMMSS_add_feature_x.sql
```

### Applying Migrations

```bash
# Local development
supabase db reset  # Resets and applies all migrations

# Push to remote
supabase db push

# Check status
supabase db diff
```

### Migration Best Practices

- One logical change per migration
- Always include RLS policies with new tables
- Use transactions for complex changes
- Test migrations locally before pushing

## Security Checklist

```
[ ] RLS enabled on ALL tables
[ ] Policies cover SELECT, INSERT, UPDATE, DELETE
[ ] Storage policies match table patterns
[ ] service_role key NEVER in client code
[ ] API keys in environment variables
[ ] Edge Functions verify auth before processing
[ ] No sensitive data in error messages
[ ] Indexes on frequently queried columns
```

## Common Commands

```bash
# Start local Supabase
supabase start

# Stop local Supabase
supabase stop

# Deploy Edge Function
supabase functions deploy function-name

# Serve Edge Function locally
supabase functions serve function-name

# Generate TypeScript types
supabase gen types typescript --local > types.ts

# View logs
supabase functions logs function-name
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bgrober) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
