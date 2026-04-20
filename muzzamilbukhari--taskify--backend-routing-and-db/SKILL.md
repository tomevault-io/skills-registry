---
name: backend-routing-and-db
description: name: backend-routing-and-db Use when this capability is needed.
metadata:
  author: muzzamilbukhari
---
---
name: backend-routing-and-db
description: Generate backend routes, handle HTTP requests/responses, and connect applications to databases.
---

# Backend Skill – Routing, Requests & Database

## Instructions

1. **Routing**
   - Create RESTful API routes
   - Use correct HTTP methods (GET, POST, PUT, DELETE)
   - Separate routes by modules/features
   - Follow clean and readable URL patterns

2. **Request & Response Handling**
   - Read request params, query, and body
   - Validate incoming data
   - Send consistent JSON responses
   - Handle errors with proper status codes

3. **Database Integration**
   - Connect backend to a database (SQL or NoSQL)
   - Use environment variables for DB credentials
   - Perform CRUD operations
   - Handle async operations safely

## Best Practices
- Use MVC or layered architecture  
- Keep controllers thin and focused  
- Centralize error handling  
- Never expose sensitive data  
- Use async/await instead of callbacks  
- Validate input before DB operations  

## Example Structure

```js
// routes/user.routes.js
import express from "express";
import { getUsers, createUser } from "../controllers/user.controller.js";

const router = express.Router();

router.get("/users", getUsers);
router.post("/users", createUser);

export default router;


// controllers/user.controller.js
export const getUsers = async (req, res) => {
  try {
    const users = await User.find();
    res.status(200).json({
      success: true,
      data: users
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: "Internal Server Error"
    });
  }
};

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/muzzamilbukhari) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
