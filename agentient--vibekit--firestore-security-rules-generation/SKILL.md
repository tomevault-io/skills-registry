---
name: firestore-security-rules-generation
description: > Use when this capability is needed.
metadata:
  author: agentient
---

# Firestore Security Rules Generation

## Overview

Firestore Security Rules define who can access what data and under what conditions. Rules are enforced at the database level, providing a critical security layer.

## Rules Syntax Fundamentals

### Basic Structure

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Rules go here
  }
}
```

### Match Patterns

```
// Exact match
match /users/alice { }

// Wildcard (single document)
match /users/{userId} { }

// Recursive wildcard (all documents in subcollections)
match /users/{userId}/{document=**} { }
```

## Authentication Patterns

### User-Scoped Access

**Pattern**: Users can only access their own data

```
match /users/{userId} {
  allow read, write: if request.auth.uid == userId;
}

match /profiles/{profileId} {
  allow read: if true; // Public read
  allow write: if request.auth.uid == resource.data.ownerId;
}
```

### Authenticated Only

```
match /posts/{postId} {
  allow read: if request.auth != null;
  allow create: if request.auth != null;
}
```

## Role-Based Access Control (RBAC)

### Custom Claims Pattern

**Set Claims** (Server-Side):
```typescript
await adminAuth.setCustomUserClaims(uid, { role: 'admin' });
```

**Rules**:
```
// Helper functions
function isAuthenticated() {
  return request.auth != null;
}

function hasRole(role) {
  return isAuthenticated() && request.auth.token.role == role;
}

function isAdmin() {
  return hasRole('admin');
}

// Admin-only collection
match /admin-data/{document} {
  allow read, write: if isAdmin();
}

// Role-based read access
match /posts/{postId} {
  allow read: if resource.data.visibility == 'public'
              || isAdmin()
              || hasRole('moderator');
}
```

## Field Validation

### Required Fields

```
function hasRequiredFields(fields) {
  return request.resource.data.keys().hasAll(fields);
}

match /posts/{postId} {
  allow create: if hasRequiredFields(['title', 'content', 'authorId', 'createdAt']);
}
```

### Data Type Validation

```
match /posts/{postId} {
  allow create: if request.resource.data.title is string
                && request.resource.data.title.size() > 0
                && request.resource.data.title.size() <= 200
                && request.resource.data.viewCount is int
                && request.resource.data.viewCount >= 0;
}
```

### Immutable Fields

```
function isImmutable(field) {
  return request.resource.data[field] == resource.data[field];
}

match /posts/{postId} {
  allow update: if isImmutable('authorId')
                && isImmutable('createdAt');
}
```

## Complete Example

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // HELPER FUNCTIONS
    function isAuthenticated() {
      return request.auth != null;
    }

    function isOwner(userId) {
      return isAuthenticated() && request.auth.uid == userId;
    }

    function hasRole(role) {
      return isAuthenticated() && request.auth.token.role == role;
    }

    function isAdmin() {
      return hasRole('admin');
    }

    function hasRequiredFields(fields) {
      return request.resource.data.keys().hasAll(fields);
    }

    function isImmutable(field) {
      return request.resource.data[field] == resource.data[field];
    }

    // USERS COLLECTION
    match /users/{userId} {
      allow read: if isOwner(userId) || isAdmin();
      allow create: if isOwner(userId)
                    && hasRequiredFields(['email', 'displayName', 'createdAt']);
      allow update: if isOwner(userId)
                    && isImmutable('createdAt')
                    && (!('role' in request.resource.data) || isImmutable('role'));
      allow delete: if isAdmin();
    }

    // POSTS COLLECTION
    match /posts/{postId} {
      allow read: if resource.data.status == 'published'
                  || isOwner(resource.data.authorId)
                  || isAdmin();

      allow create: if isAuthenticated()
                    && request.resource.data.authorId == request.auth.uid
                    && hasRequiredFields(['title', 'content', 'authorId', 'status', 'createdAt'])
                    && request.resource.data.status in ['draft', 'published', 'archived']
                    && request.resource.data.title is string
                    && request.resource.data.title.size() > 0
                    && request.resource.data.title.size() <= 200;

      allow update: if (isOwner(resource.data.authorId) || isAdmin())
                    && isImmutable('authorId')
                    && isImmutable('createdAt');

      allow delete: if isOwner(resource.data.authorId) || isAdmin();
    }
  }
}
```

## Deployment

```bash
# Deploy rules only
firebase deploy --only firestore:rules

# Validate rules before deploying
firebase firestore:rules:release
```

## Best Practices

**Do**:
- Default deny, explicitly allow
- Validate all user input (types, sizes, ranges)
- Use helper functions for reusable logic
- Test rules locally before deploying
- Document complex rules with comments
- Use custom claims for RBAC (not Firestore lookups)
- Enforce immutable fields (createdAt, authorId)

**Don't**:
- Use `allow read, write: if true` in production
- Perform Firestore lookups in rules (slow, limited to 10 per request)
- Store sensitive data in custom claims (1000 byte limit)
- Skip field validation
- Use rules for rate limiting (use Cloud Functions)

---

**Related Skills**: `firebase-authentication-patterns`, `firebase-admin-sdk-server-integration`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentient) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
