---
name: validate-auth-flow
description: Validate authentication and authorization implementation for security and correctness Use when this capability is needed.
metadata:
  author: luanps2
---

# Validate Auth Flow Skill

## Authentication Validation Checklist

### Password-Based Auth
- [ ] Passwords hashed with bcrypt/argon2 (never plain text)
- [ ] Minimum password strength enforced
- [ ] Rate limiting on login attempts
- [ ] Account lockout after failed attempts
- [ ] Secure password reset flow with expiring tokens

### Token-Based Auth (JWT)
- [ ] JWT signed with strong secret
- [ ] Expiration time set (recommended: 15min-1hour)
- [ ] Refresh tokens implemented for long sessions
- [ ] Tokens validated on every protected route
- [ ] Token stored securely (httpOnly cookies, not localStorage)

### OAuth (Google, Microsoft, GitHub)
- [ ] Client ID and secret secured
- [ ] Redirect URIs whitelisted
- [ ] State parameter for CSRF protection
- [ ] User consent handling
- [ ] Token exchange secure

### Session Management
- [ ] Session IDs cryptographically random
- [ ] Session expiration implemented
- [ ] Session invalidation on logout
- [ ] Secure cookie flags (httpOnly, secure, sameSite)

## Authorization Validation

### Role-Based Access Control (RBAC)
- [ ] User roles defined (admin, user, guest)
- [ ] Permission checks on protected routes
- [ ] Resource ownership validated
- [ ] Principle of least privilege followed

### Example Auth Middleware
```typescript
export function requireAuth(req: Request, res: Response, next: NextFunction) {
  const token = req.cookies.access_token || req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  
  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET!);
    req.user = payload;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
}
```

## Security Best Practices

1. **HTTPS Only**: All auth endpoints over HTTPS
2. **CSRF Protection**: Use CSRF tokens for state-changing requests
3. **Rate Limiting**: Prevent brute force attacks
4. **Input Validation**: Sanitize all inputs
5. **Error Messages**: Don't leak information ("Invalid credentials" not "User not found")

## Related Skills

- `review_security` - Comprehensive security review
- `create_api_endpoint` - Secure API endpoints

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luanps2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
