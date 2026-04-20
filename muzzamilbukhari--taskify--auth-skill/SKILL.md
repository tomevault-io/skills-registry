---
name: auth-skill
description: description: Implement secure authentication systems including signup, signin, password hashing, JWT tokens, and Better Auth integration. Use when this capability is needed.
metadata:
  author: muzzamilbukhari
---
---
name: auth-skill
description: Implement secure authentication systems including signup, signin, password hashing, JWT tokens, and Better Auth integration.
---

# Authentication & Authorization Skill

## Instructions

### 1. Core Authentication Flow
- User signup with proper validation
- User signin using credentials
- Secure logout mechanism
- Session-based or token-based authentication

### 2. Password Security
- Hash passwords using **bcrypt** or **argon2**
- Never store plain-text passwords
- Secure password comparison
- Optional password reset & forgot-password flow

### 3. JWT Token Handling
- Generate access tokens on signin
- Store tokens securely (HTTP-only cookies preferred)
- Verify JWT on protected routes
- Implement token expiration & refresh logic

### 4. Better Auth Integration
- Configure Better Auth provider
- Integrate with Express.js or Next.js
- Support credentials & OAuth providers
- Centralized authentication configuration

## Security Best Practices
- Store secrets in environment variables
- Apply rate limiting on auth endpoints
- Validate & sanitize all inputs
- Enforce HTTPS
- Use least-privilege access control
- Protect routes using middleware

## Example Structure

```ts
// signup.ts
import bcrypt from "bcrypt";
import jwt from "jsonwebtoken";

export async function signup(req, res) {
  const { email, password } = req.body;

  const hashedPassword = await bcrypt.hash(password, 12);

  const user = await db.user.create({
    data: { email, password: hashedPassword }
  });

  const token = jwt.sign(
    { userId: user.id },
    process.env.JWT_SECRET!,
    { expiresIn: "1h" }
  );

  res.cookie("token", token, { httpOnly: true });
  res.status(201).json({ message: "User created successfully" });
}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/muzzamilbukhari) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
