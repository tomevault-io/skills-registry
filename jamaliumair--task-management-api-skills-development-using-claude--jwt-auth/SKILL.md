---
name: jwt-auth
description: | Use when this capability is needed.
metadata:
  author: jamaliumair
---

# JWT Authentication Skill

Comprehensive JWT authentication patterns for Python APIs (FastAPI, Flask, etc.).

## Quick Reference

| Feature | Reference File |
|---------|----------------|
| Token creation, validation, refresh | [references/tokens.md](references/tokens.md) |
| Password hashing (Argon2, bcrypt) | [references/password-hashing.md](references/password-hashing.md) |
| Protected routes, RBAC | [references/protected-routes.md](references/protected-routes.md) |

## Dependencies

```toml
[project]
dependencies = [
    "python-jose[cryptography]>=3.3.0",  # JWT encoding/decoding
    "passlib[bcrypt]>=1.7.4",            # Password hashing
    "argon2-cffi>=23.1.0",               # Argon2 (recommended)
]
```

## JWT Basics

### Token Structure

```
header.payload.signature

Header:  {"alg": "HS256", "typ": "JWT"}
Payload: {"sub": "user_id", "exp": 1234567890, "iat": 1234567800}
Signature: HMACSHA256(base64(header) + "." + base64(payload), secret)
```

### Core Security Module

```python
from datetime import datetime, timedelta, timezone
from jose import JWTError, jwt
from passlib.context import CryptContext

SECRET_KEY = "your-secret-key-min-32-chars"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

pwd_context = CryptContext(schemes=["argon2", "bcrypt"], deprecated="auto")

def verify_password(plain: str, hashed: str) -> bool:
    return pwd_context.verify(plain, hashed)

def hash_password(password: str) -> str:
    return pwd_context.hash(password)

def create_access_token(data: dict, expires_delta: timedelta | None = None) -> str:
    to_encode = data.copy()
    expire = datetime.now(timezone.utc) + (expires_delta or timedelta(minutes=15))
    to_encode.update({"exp": expire, "type": "access"})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

def decode_token(token: str) -> dict | None:
    try:
        return jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
    except JWTError:
        return None
```

## FastAPI Integration

### OAuth2 Password Bearer

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/auth/login")

async def get_current_user(token: str = Depends(oauth2_scheme)):
    payload = decode_token(token)
    if not payload or payload.get("type") != "access":
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token",
            headers={"WWW-Authenticate": "Bearer"},
        )
    user_id = payload.get("sub")
    # Fetch user from database
    return user
```

### Login Endpoint

```python
from fastapi.security import OAuth2PasswordRequestForm

@router.post("/login")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    user = await get_user_by_email(form_data.username)
    if not user or not verify_password(form_data.password, user.hashed_password):
        raise HTTPException(401, "Invalid credentials")

    return {
        "access_token": create_access_token({"sub": str(user.id)}),
        "token_type": "bearer"
    }
```

## Token Pair Pattern (Access + Refresh)

```python
def create_token_pair(user_id: int) -> dict:
    return {
        "access_token": create_access_token({"sub": str(user_id)}),
        "refresh_token": create_refresh_token({"sub": str(user_id)}),
        "token_type": "bearer",
    }

def create_refresh_token(data: dict) -> str:
    to_encode = data.copy()
    expire = datetime.now(timezone.utc) + timedelta(days=7)
    to_encode.update({"exp": expire, "type": "refresh"})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

@router.post("/refresh")
async def refresh(refresh_token: str):
    payload = decode_token(refresh_token)
    if not payload or payload.get("type") != "refresh":
        raise HTTPException(401, "Invalid refresh token")
    return create_token_pair(int(payload["sub"]))
```

## Environment Variables

```env
JWT_SECRET_KEY=your-super-secret-key-at-least-32-characters
JWT_ALGORITHM=HS256
JWT_ACCESS_TOKEN_EXPIRE_MINUTES=30
JWT_REFRESH_TOKEN_EXPIRE_DAYS=7
```

## Security Best Practices

1. **Secret Key**: Use 256+ bit random key, never commit to git
2. **Token Expiry**: Short-lived access tokens (15-30 min), longer refresh tokens
3. **HTTPS Only**: Always use HTTPS in production
4. **Password Hashing**: Use Argon2 (preferred) or bcrypt, never SHA/MD5
5. **Token Storage**: Store in httpOnly cookies or secure storage, not localStorage

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jamaliumair) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
