---
name: auth-skill
description: description: Implement secure authentication systems including signup, signin, password hashing, JWT tokens, and Better Auth integration. Use when this capability is needed.
metadata:
  author: eraj-ns
---
---
name: auth-skill
description: Implement secure authentication systems including signup, signin, password hashing, JWT tokens, and Better Auth integration.
---

# Authentication Skill

## Instructions

1. **User Registration (Signup)**
   - Collect user credentials (email/username, password)
   - Validate input fields
   - Hash passwords before storing
   - Store user securely in database

2. **User Login (Signin)**
   - Verify user credentials
   - Compare hashed passwords
   - Handle invalid login attempts
   - Generate authentication token on success

3. **Password Security**
   - Use strong hashing algorithms (bcrypt / argon2)
   - Add salt to passwords
   - Never store plain-text passwords
   - Implement password reset flow

4. **JWT Authentication**
   - Generate JWT tokens on login
   - Include user ID and roles in payload
   - Set token expiration
   - Verify tokens on protected routes

5. **Better Auth Integration**
   - Centralize authentication logic
   - Support token refresh
   - Role-based access control
   - Secure session handling

## Best Practices
- Use HTTPS for all auth routes
- Set strong password policies
- Store JWT in HTTP-only cookies
- Implement token expiration & refresh
- Log authentication events securely

## Example Structure
```js
// Signup
app.post("/signup", async (req, res) => {
  const hashedPassword = await bcrypt.hash(req.body.password, 10);
  // save user
});

// Signin
app.post("/signin", async (req, res) => {
  const token = jwt.sign({ userId }, process.env.JWT_SECRET, {
    expiresIn: "1h",
  });
  res.json({ token });
});

// Protected Route
app.get("/profile", verifyJWT, (req, res) => {
  res.send("Protected Data");
});

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eraj-ns) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
