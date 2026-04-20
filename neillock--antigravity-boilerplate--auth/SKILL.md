---
name: authentication-account-management
description: Comprehensive standards for implementing secure authentication using Google OAuth 2.0 and NextAuth.js. Use when this capability is needed.
metadata:
  author: neillock
---

# Authentication & Account Management Skill

This skill defines **mandatory** standards for implementing secure, production-grade authentication in Antigravity projects. All implementations MUST follow Google's OAuth 2.0 best practices.

---

## 1. Core Principles

| Principle | Requirement |
|-----------|-------------|
| **SSO First** | Prefer Google OAuth 2.0 over custom credentials |
| **Least Privilege** | Request only the minimum scopes required |
| **Defense in Depth** | Validate on both client and server |
| **Secure by Default** | All protected routes require authentication |

---

## 2. Google OAuth 2.0 Best Practices (MANDATORY)

### 2.1 Credential Security
- **NEVER** commit `GOOGLE_CLIENT_ID` or `GOOGLE_CLIENT_SECRET` to version control.
- Store secrets in a secure manager (e.g., GCP Secret Manager, Vercel Environment Variables).
- Rotate secrets immediately if exposed.
- Use separate OAuth clients for **Development**, **Staging**, and **Production**.

### 2.2 Token Handling
| Rule | Implementation |
|------|----------------|
| **No Plaintext Tokens** | Never transmit tokens over HTTP; HTTPS only |
| **Encrypt at Rest** | Store tokens encrypted in the database |
| **Short-Lived Access** | Use short-lived access tokens (default: 1 hour) |
| **Secure Refresh** | Store refresh tokens server-side only |
| **Revoke on Logout** | Invalidate tokens when user logs out or revokes permissions |

### 2.3 Scope Management
- **Incremental Authorization**: Request scopes only when the specific feature needs them.
- **Graceful Denial**: Handle scope denial gracefully by disabling affected features.
- **Default Scopes**: For basic auth, use only `openid`, `profile`, and `email`.

### 2.4 Redirect URI Validation
- All redirect URIs MUST use HTTPS (except `localhost` for dev).
- Define explicit, allowed redirect URIs in Google Cloud Console.
- Never accept dynamic or user-supplied redirect URIs.

---

## 3. Route Protection Patterns

Every route MUST be classified as one of the following:

### 3.1 Public Routes (Unauthenticated)
No authentication required. Accessible by anyone.

| Example Routes | Description |
|----------------|-------------|
| `/` | Landing page |
| `/login` | Login page |
| `/about` | Static pages |
| `/api/health` | Health check endpoint |

### 3.2 Protected Routes (Authenticated)
Require a valid session. Redirect to `/login` if unauthenticated.

| Example Routes | Description |
|----------------|-------------|
| `/dashboard` | User's main dashboard |
| `/profile` | User settings |
| `/api/user/*` | User-specific API endpoints |

### 3.3 Admin Routes (Authorized)
Require authentication AND the `admin` role. Return 403 if not authorized.

| Example Routes | Description |
|----------------|-------------|
| `/admin/*` | Admin panel pages |
| `/api/admin/*` | Admin API endpoints |

---

## 4. API Endpoint Security

All backend API endpoints MUST declare their authentication status.

### 4.1 Classification Table (Template)
When creating API endpoints, document them as follows:

| Endpoint | Method | Auth Required | Role Required | Rate Limited |
|----------|--------|---------------|---------------|--------------|
| `/api/health` | GET | ❌ No | None | ❌ |
| `/api/user/profile` | GET | ✅ Yes | `user` | ✅ 100/min |
| `/api/admin/users` | GET | ✅ Yes | `admin` | ✅ 30/min |
| `/api/admin/users/:id` | DELETE | ✅ Yes | `admin` | ✅ 10/min |

### 4.2 Implementation Requirements
1. **Server-Side Validation**: NEVER trust client-side auth state. Always validate the session on the server.
2. **Use Middleware**: Protect routes via Next.js Middleware or Express middleware.
3. **Role-Based Access Control (RBAC)**: Check user roles in the database, not in JWT claims alone.
4. **Error Responses**:
   - `401 Unauthorized` → User not authenticated
   - `403 Forbidden` → User authenticated but lacks required role

---

## 5. Account Management

### 5.1 Profile Page (`/profile`)
Every authenticated application MUST have a profile page with:
- Display user's name, email, and profile picture from Google
- Option to log out
- Option to delete account (with confirmation)

### 5.2 Session Management
| Setting | Recommended Value |
|---------|-------------------|
| Session Duration | 30 days (rolling) |
| Inactivity Timeout | 7 days |
| Remember Me | Optional, extends to 90 days |

### 5.3 Account Deletion
- Provide a clear way for users to request account deletion.
- Revoke all tokens and delete user data upon deletion.
- Confirm deletion via email or re-authentication.

---

## 6. Implementation Checklist

### Credential Setup
- [ ] Registered OAuth 2.0 client in Google Cloud Console
- [ ] Configured separate clients for Dev/Staging/Prod
- [ ] Stored `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` in secure environment
- [ ] Generated `NEXTAUTH_SECRET` securely (`openssl rand -base64 32`)

### Route Protection
- [ ] Middleware configured for protected routes
- [ ] All API endpoints documented with auth requirements (see §4.1)
- [ ] Admin routes check role in database

### Token Security
- [ ] Tokens never exposed to client-side JavaScript
- [ ] Refresh tokens stored server-side only
- [ ] Tokens revoked on logout

### Validation
- [ ] Tested login flow end-to-end
- [ ] Tested logout flow including token revocation
- [ ] Tested unauthorized access returns 401/403
- [ ] Tested admin route protection with non-admin user

---

## 7. NextAuth.js Configuration Template

```typescript
// auth.config.ts
import NextAuth from "next-auth"
import Google from "next-auth/providers/google"

export const { handlers, auth, signIn, signOut } = NextAuth({
  providers: [
    Google({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
      authorization: {
        params: {
          prompt: "consent",
          access_type: "offline",
          response_type: "code",
        },
      },
    }),
  ],
  callbacks: {
    async session({ session, token }) {
      if (session.user) {
        session.user.id = token.sub as string
        // Fetch role from database, NOT from token
      }
      return session
    },
    async jwt({ token, account }) {
      if (account) {
        token.accessToken = account.access_token
      }
      return token
    },
  },
  pages: {
    signIn: "/login",
    error: "/auth/error",
  },
  session: {
    strategy: "jwt",
    maxAge: 30 * 24 * 60 * 60, // 30 days
  },
})
```

---

## 8. Workflow Triggers

- **Backend Logic in Client**: If writing auth logic in a Client Component → STOP. Move to Server Action or API route.
- **Hardcoded Secrets Detected**: If `GOOGLE_CLIENT_*` is hardcoded → STOP. Move to environment variables.
- **Missing Middleware**: If creating new protected routes without middleware → STOP. Add to middleware matcher.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neillock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
