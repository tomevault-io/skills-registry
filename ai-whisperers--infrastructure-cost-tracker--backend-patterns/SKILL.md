---
name: backend-patterns
description: Backend architecture patterns and best practices. Use when designing APIs, databases, microservices, or server-side systems. Use when this capability is needed.
metadata:
  author: ai-whisperers
---

# Backend Patterns Skill

Backend architecture patterns and best practices for designing scalable, maintainable server-side systems.

## When to Use

- Designing APIs
- Architecting databases
- Building microservices
- Implementing caching strategies
- Setting up authentication

## API Design Patterns

### RESTful API

```
GET    /api/users          # List users
GET    /api/users/{id}     # Get user
POST   /api/users          # Create user
PUT    /api/users/{id}     # Update user
DELETE /api/users/{id}     # Delete user
```

**Principles:**
- Stateless communication
- Resource-based URLs
- HTTP verbs for actions
- JSON for data exchange
- Proper status codes

### GraphQL API

```graphql
type Query {
  user(id: ID!): User
  users(limit: Int): [User]
}

type Mutation {
  createUser(input: CreateUserInput!): User
  updateUser(id: ID!, input: UpdateUserInput!): User
}
```

**Benefits:**
- Client-specified queries
- Single endpoint
- Strong typing
- Reduced over-fetching

## Database Patterns

### Repository Pattern

```python
class UserRepository:
    def __init__(self, db):
        self.db = db
    
    def get_by_id(self, id: int) -> User:
        return self.db.query(User).filter_by(id=id).first()
    
    def create(self, user: User) -> User:
        self.db.add(user)
        self.db.commit()
        return user
```

### Unit of Work

```python
class UnitOfWork:
    def __init__(self):
        self.session = Session()
        self.users = UserRepository(self.session)
        self.orders = OrderRepository(self.session)
    
    def commit(self):
        self.session.commit()
    
    def rollback(self):
        self.session.rollback()
```

## Authentication Patterns

### JWT Authentication

```python
import jwt
from datetime import datetime, timedelta

def create_token(user_id: str, secret: str) -> str:
    payload = {
        'user_id': user_id,
        'exp': datetime.utcnow() + timedelta(hours=24),
        'iat': datetime.utcnow()
    }
    return jwt.encode(payload, secret, algorithm='HS256')

def verify_token(token: str, secret: str) -> dict:
    try:
        return jwt.decode(token, secret, algorithms=['HS256'])
    except jwt.ExpiredSignatureError:
        raise ValueError("Token expired")
```

### OAuth2 Flow

1. User authorizes app
2. App receives authorization code
3. App exchanges code for access token
4. App uses token to access resources

## Caching Patterns

### Cache-Aside

```python
def get_user(user_id: str) -> User:
    # Try cache first
    user = cache.get(f"user:{user_id}")
    if user:
        return user
    
    # Load from database
    user = db.query(User).get(user_id)
    if user:
        cache.set(f"user:{user_id}", user, ttl=3600)
    
    return user
```

### Write-Through

```python
def update_user(user_id: str, data: dict) -> User:
    # Update database
    user = db.query(User).get(user_id)
    user.update(data)
    db.commit()
    
    # Update cache
    cache.set(f"user:{user_id}", user, ttl=3600)
    
    return user
```

## Error Handling

### Structured Error Response

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  }
}
```

### HTTP Status Codes

| Code | Meaning | Usage |
|------|---------|-------|
| 200 | OK | Successful GET/PUT |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Validation error |
| 401 | Unauthorized | Missing auth |
| 403 | Forbidden | No permission |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Resource already exists |
| 422 | Unprocessable | Semantic error |
| 500 | Server Error | Unexpected error |

## Microservices Patterns

### API Gateway

- Single entry point
- Authentication
- Rate limiting
- Request routing
- Response aggregation

### Circuit Breaker

```python
from circuitbreaker import circuit

@circuit(failure_threshold=5, recovery_timeout=60)
def call_external_service():
    return requests.get("https://api.example.com/data")
```

### Event Sourcing

- Store events, not state
- Rebuild state from events
- Audit trail
- Temporal queries

## Security Best Practices

1. **Input Validation**: Validate all inputs
2. **Parameterized Queries**: Prevent SQL injection
3. **HTTPS Only**: Encrypt all traffic
4. **Rate Limiting**: Prevent abuse
5. **CORS**: Configure properly
6. **Secrets Management**: Use environment variables
7. **Logging**: Log security events
8. **Dependencies**: Keep updated

## Performance Optimization

1. **Database Indexing**: Index frequently queried columns
2. **Connection Pooling**: Reuse connections
3. **Async Operations**: Use async/await
4. **Compression**: Compress responses
5. **CDN**: Serve static assets
6. **Lazy Loading**: Load data on demand

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-whisperers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
