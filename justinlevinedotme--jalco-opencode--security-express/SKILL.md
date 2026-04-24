---
name: security-express
description: |- Use when this capability is needed.
metadata:
  author: justinlevinedotme
---

<overview>

Security audit patterns for Express.js applications covering essential security middleware, CORS configuration, auth patterns, and common vulnerabilities.

</overview>

<rules>

## Essential Security Middleware

### Helmet.js (Security Headers)
```javascript
// Missing security headers - MUST NOT do this
const app = express();

// MUST use Helmet
const helmet = require('helmet');
app.use(helmet());
```

**MUST check if Helmet is installed and used.** It sets:
- Content-Security-Policy
- X-Content-Type-Options: nosniff
- X-Frame-Options: DENY
- Strict-Transport-Security
- And more...

### Disable X-Powered-By
```javascript
// Default - header reveals framework
const app = express();

// MUST disable fingerprinting
app.disable('x-powered-by');
// or: app.set('x-powered-by', false);
```

### CORS Configuration
```javascript
// CRITICAL: Allow all origins - MUST NOT do this
app.use(cors());
app.use(cors({ origin: '*' }));

// HIGH: Reflect origin with credentials - MUST NOT do this
app.use(cors({ 
  origin: true,  // Reflects any origin!
  credentials: true 
}));

// MUST use explicit allowlist
app.use(cors({
  origin: ['https://app.example.com', 'https://admin.example.com'],
  credentials: true,
}));

// MAY use function for dynamic validation
app.use(cors({
  origin: (origin, callback) => {
    const allowed = ['https://app.example.com'];
    if (!origin || allowed.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
}));
```

## Body Parser Limits

```javascript
// No limit (DoS risk) - MUST NOT do this
app.use(express.json());

// MUST set reasonable limits
app.use(express.json({ limit: '100kb' }));
app.use(express.urlencoded({ extended: true, limit: '100kb' }));
```

</rules>

<vulnerabilities>

## Auth Middleware Patterns

### Missing Auth on Routes
```javascript
// No auth on admin routes - MUST NOT do this
app.get('/api/admin/users', async (req, res) => {
  res.json(await User.find());
});

// MUST apply auth middleware
app.get('/api/admin/users', requireAuth, requireAdmin, async (req, res) => {
  res.json(await User.find());
});
```

### Middleware Order Matters
```javascript
// Wrong order - static files before auth - MUST NOT do this
app.use(express.static('uploads')); // Exposed!
app.use(requireAuth);

// MUST place auth before protected static files
app.use('/public', express.static('public')); // Intentionally public
app.use(requireAuth);
app.use('/uploads', express.static('uploads')); // Now protected
```

### Router-Level Auth Gaps
```javascript
// SHOULD check: Is auth applied to all routes in admin router?
const adminRouter = express.Router();
adminRouter.use(requireAuth); // Applied to all routes below
adminRouter.get('/users', getUsers);
adminRouter.delete('/users/:id', deleteUser);

// Watch for routes defined BEFORE the middleware
const apiRouter = express.Router();
apiRouter.get('/health', getHealth); // No auth (intentional?)
apiRouter.use(requireAuth);
apiRouter.get('/users', getUsers); // Has auth
```

## Common Vulnerabilities

### SQL/NoSQL Injection
```javascript
// String interpolation - MUST NOT do this
const user = await db.query(`SELECT * FROM users WHERE id = ${req.params.id}`);

// MUST use parameterized query
const user = await db.query('SELECT * FROM users WHERE id = $1', [req.params.id]);

// MongoDB injection risk
const user = await User.findOne({ email: req.body.email }); // If email is { $gt: "" }

// MUST validate input type
if (typeof req.body.email !== 'string') return res.status(400).json({ error: 'Invalid email' });
```

### Path Traversal
```javascript
// User-controlled path - MUST NOT do this
app.get('/files/:filename', (req, res) => {
  res.sendFile(`./uploads/${req.params.filename}`); // ../../etc/passwd
});

// MUST validate and normalize
const path = require('path');
app.get('/files/:filename', (req, res) => {
  const filename = path.basename(req.params.filename);
  const filepath = path.join(__dirname, 'uploads', filename);
  if (!filepath.startsWith(path.join(__dirname, 'uploads'))) {
    return res.status(400).json({ error: 'Invalid path' });
  }
  res.sendFile(filepath);
});
```

### Error Handling
```javascript
// Stack traces in production - MUST NOT do this
app.use((err, req, res, next) => {
  res.status(500).json({ error: err.stack }); // Leaks internals
});

// MUST use safe error handler
app.use((err, req, res, next) => {
  console.error(err); // Log for debugging
  res.status(500).json({ error: 'Internal server error' });
});
```

### Session Security
```javascript
// Insecure session config - MUST NOT do this
app.use(session({
  secret: 'keyboard cat', // Hardcoded!
  cookie: { secure: false }, // No HTTPS requirement
}));

// MUST use secure config
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    secure: true, // HTTPS only
    httpOnly: true, // No JS access
    sameSite: 'strict', // CSRF protection
    maxAge: 1000 * 60 * 60 * 24, // 24 hours
  },
}));
```

## Rate Limiting

SHOULD check for rate limiting on auth routes:

```javascript
const rateLimit = require('express-rate-limit');

const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // 5 attempts
  message: 'Too many login attempts',
});

app.post('/api/login', authLimiter, loginHandler);
app.post('/api/register', authLimiter, registerHandler);
app.post('/api/forgot-password', authLimiter, forgotPasswordHandler);
```

</vulnerabilities>

<commands>

## Quick Audit Commands

```bash
# Check if Helmet is used
rg -n 'helmet\\(' . -g "*.js" -g "*.ts"

# Check if x-powered-by is disabled
rg -n "x-powered-by" . -g "*.js" -g "*.ts"
```

```bash
# Check for helmet
rg "helmet" package.json
rg "require\\(['\"]helmet" .
rg "from ['\"]helmet" .

# Find CORS config
rg "cors\\(" . -g "*.js" -g "*.ts" -A 5

# Find routes without auth middleware
rg "app\\.(get|post|put|delete|patch)\\(" . -A 1 | grep -v "require.*[Aa]uth"

# Find string interpolation in queries
rg "(query|find|findOne|exec).*\\`" . -g "*.js" -g "*.ts"

# Check session config
rg "session\\(" . -A 10
```

</commands>

<checklist>

## Hardening Checklist

- [ ] Helmet.js MUST be installed and used
- [ ] CORS MUST be restricted to specific origins
- [ ] Body parser MUST have size limits
- [ ] Auth middleware MUST be on all protected routes
- [ ] Rate limiting SHOULD be on auth endpoints
- [ ] Session cookies MUST be: secure, httpOnly, sameSite
- [ ] MUST NOT have hardcoded secrets
- [ ] Error handler MUST NOT leak stack traces
- [ ] MUST have input validation on all user input
- [ ] MUST use parameterized queries (no string concat)

</checklist>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinlevinedotme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
