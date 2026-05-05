---
name: go-backend-clean-architecture
description: Go backend with Gin, MongoDB, JWT auth, and Clean Architecture. Use when this capability is needed.
metadata:
  author: neversight
---

# Go Clean Architecture

A Go backend with Gin, MongoDB, JWT authentication, and Docker support following Clean Architecture principles.

## Tech Stack

- **Framework**: Gin
- **Language**: Go
- **Database**: MongoDB
- **Auth**: JWT
- **Architecture**: Clean Architecture

## Prerequisites

- Go 1.21+
- MongoDB
- Docker (optional)

## Setup

### 1. Clone the Template

```bash
git clone --depth 1 https://github.com/amitshekhariitbhu/go-backend-clean-architecture.git .
```

If the directory is not empty:

```bash
git clone --depth 1 https://github.com/amitshekhariitbhu/go-backend-clean-architecture.git _temp_template
mv _temp_template/* _temp_template/.* . 2>/dev/null || true
rm -rf _temp_template
```

### 2. Remove Git History (Optional)

```bash
rm -rf .git
git init
```

### 3. Install Dependencies

```bash
go mod download
```

### 4. Setup Environment

Configure MongoDB connection and JWT secret.

## Build

```bash
go build -o app ./cmd/main.go
```

## Development

```bash
go run ./cmd/main.go
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
