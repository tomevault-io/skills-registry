---
name: configure-social-providers
description: Set up social authentication providers (Google, GitHub, etc.) with proper OAuth configuration Use when this capability is needed.
metadata:
  author: aqsagull99
---

# Social Provider Configuration

## Instructions
1. Identify which social providers need to be configured (Google, GitHub, Discord, etc.)
2. Register the application with each provider to get client credentials
3. Add the provider configuration to the Better Auth setup
4. Set up proper redirect URLs
5. Handle provider-specific options
6. Test the social authentication flows

## Supported Providers
Better Auth supports multiple social providers including:
- Google
- GitHub
- Discord
- Facebook
- Twitter/X
- Apple
- And more

## Configuration Steps
1. Go to the provider's developer portal and create an application
2. Set the redirect URI to your app's callback URL (typically `/api/auth/callback/[provider]`)
3. Get the Client ID and Client Secret
4. Add the provider to your Better Auth configuration
5. Add the credentials to environment variables
6. Test the authentication flow

## Example Configuration
```typescript
import { betterAuth } from "better-auth";
import { google, github } from "better-auth/oauth-providers";

export const auth = betterAuth({
  // other options...
  socialProviders: {
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    },
    github: {
      clientId: process.env.GITHUB_CLIENT_ID!,
      clientSecret: process.env.GITHUB_CLIENT_SECRET!,
    },
  },
});
```

## Provider-Specific Considerations
- Some providers require domain verification
- Callback URLs must match exactly what's registered
- Some providers have special requirements (Apple requires additional configuration)
- Store credentials securely in environment variables
- Consider using different credentials for development and production

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aqsagull99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
