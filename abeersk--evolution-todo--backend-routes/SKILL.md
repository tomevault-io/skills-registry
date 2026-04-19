---
name: backend-routes
description: Generate backend routes, handle HTTP requests/responses, and connect to a database. Use for building APIs. Use when this capability is needed.
metadata:
  author: abeersk
---

# Backend Routes & DB Handling

## Instructions

1. **Route Setup**
   - Define RESTful endpoints (`GET`, `POST`, `PUT`, `DELETE`)
   - Use clear route naming conventions (`/users`, `/tasks`, `/products`)
   - Group related routes logically

2. **Request & Response Handling**
   - Parse incoming requests (`req.body`, `req.params`, `req.query`)
   - Send structured responses (`status code`, `JSON payload`)
   - Handle errors gracefully with informative messages

3. **Database Connection**
   - Connect to your database (SQL or NoSQL)
   - Perform CRUD operations efficiently
   - Ensure proper connection pooling and error handling

## Best Practices
- Keep route handlers small and modular
- Validate user input to prevent errors or attacks
- Use middleware for authentication, logging, and error handling
- Maintain consistent response structure
- Write tests for critical routes

## Example Structure
```javascript
// server.js
import express from 'express';
import { connectDB } from './db.js';
import userRoutes from './routes/users.js';

const app = express();
app.use(express.json());

// Connect to Database
connectDB();

// Use Routes
app.use('/users', userRoutes);

app.listen(3000, () => {
  console.log('Server running on port 3000');
});

// routes/users.js
import express from 'express';
const router = express.Router();

// GET /users
router.get('/', async (req, res) => {
  try {
    const users = await getUsersFromDB();
    res.status(200).json(users);
  } catch (err) {
    res.status(500).json({ error: 'Server error' });
  }
});

// POST /users
router.post('/', async (req, res) => {
  try {
    const newUser = await createUserInDB(req.body);
    res.status(201).json(newUser);
  } catch (err) {
    res.status(400).json({ error: 'Invalid data' });
  }
});

export default router;

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abeersk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
