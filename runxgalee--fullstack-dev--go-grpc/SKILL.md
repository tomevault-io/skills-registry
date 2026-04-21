---
name: go-grpc
description: Analyze Go gRPC projects using Protocol Buffers. Use when onboarding to Go gRPC services, understanding proto definitions, analyzing service implementations, reviewing interceptors/middleware, examining streaming patterns, identifying client/server patterns, and generating gRPC API documentation. Use when this capability is needed.
metadata:
  author: runxgalee
---

## Purpose

Provide comprehensive analysis of Go gRPC projects to help developers quickly understand service definitions, RPC implementations, interceptor chains, and streaming patterns. This skill leverages the grpc-specialist agent for deep gRPC analysis.

## When to Use

Use this skill when you need to:

- **Onboard to a gRPC project** - Understand the full service architecture
- **Analyze proto definitions** - Review service, message, and enum definitions
- **Review service implementations** - Understand how RPCs are implemented
- **Examine interceptors** - Analyze middleware chains (auth, logging, tracing)
- **Understand streaming** - Analyze server/client/bidirectional streaming
- **Document the gRPC API** - Generate service documentation
- **Identify patterns** - Find error handling, validation, and retry patterns

## gRPC Detection

### Identifying gRPC Projects

Check for gRPC markers:

```bash
# Check go.mod for gRPC
grep -E "google.golang.org/grpc|google.golang.org/protobuf" go.mod

# Find proto files
find . -name "*.proto"

# Find generated pb.go files
find . -name "*.pb.go" -o -name "*_grpc.pb.go"

# Find buf configuration
find . -name "buf.yaml" -o -name "buf.gen.yaml"
```

### gRPC Project Structure

```
project/
├── api/
│   └── proto/
│       ├── user/
│       │   └── v1/
│       │       └── user.proto
│       └── product/
│           └── v1/
│               └── product.proto
├── gen/
│   └── go/
│       ├── user/v1/
│       │   ├── user.pb.go
│       │   └── user_grpc.pb.go
│       └── product/v1/
│           ├── product.pb.go
│           └── product_grpc.pb.go
├── internal/
│   ├── server/
│   │   ├── user_server.go
│   │   └── product_server.go
│   └── interceptor/
│       ├── auth.go
│       └── logging.go
├── cmd/
│   └── server/
│       └── main.go
├── buf.yaml
└── buf.gen.yaml
```

## Analysis Checklist

### 1. Proto File Analysis

```bash
# Find all proto files
find . -name "*.proto"

# Count services
grep -rn "^service " --include="*.proto"

# Count RPCs per service
grep -rn "rpc " --include="*.proto"

# Find message definitions
grep -rn "^message " --include="*.proto"

# Find enum definitions
grep -rn "^enum " --include="*.proto"

# Find streaming RPCs
grep -rn "stream " --include="*.proto"
```

### 2. Service Implementation Analysis

```bash
# Find service implementations
grep -rn "type.*Server struct" --include="*.go"

# Find RPC method implementations
grep -rn "func.*Server\).*context.Context" --include="*.go"

# Find server registration
grep -rn "Register.*Server\|pb.Register" --include="*.go"
```

### 3. Interceptor Analysis

```bash
# Find unary interceptors
grep -rn "UnaryServerInterceptor\|UnaryClientInterceptor" --include="*.go"

# Find stream interceptors
grep -rn "StreamServerInterceptor\|StreamClientInterceptor" --include="*.go"

# Find interceptor chain setup
grep -rn "ChainUnaryInterceptor\|ChainStreamInterceptor" --include="*.go"
```

### 4. Streaming Pattern Analysis

```bash
# Find server streaming implementations
grep -rn "func.*Server\).*stream" --include="*.go"

# Find client streaming usage
grep -rn "\.Send\|\.Recv\|\.SendAndClose\|\.CloseAndRecv" --include="*.go"

# Find bidirectional streaming
grep -rn "stream.*stream" --include="*.proto"
```

### 5. Error Handling Analysis

```bash
# Find gRPC status codes
grep -rn "codes\.\|status\." --include="*.go"

# Find error wrapping
grep -rn "status.Error\|status.Errorf" --include="*.go"

# Find error details
grep -rn "WithDetails\|errdetails" --include="*.go"
```

### 6. Client Analysis

```bash
# Find client connections
grep -rn "grpc.Dial\|grpc.NewClient" --include="*.go"

# Find client creation
grep -rn "New.*Client\|pb.*Client" --include="*.go"

# Find call options
grep -rn "grpc.WithInsecure\|grpc.WithTransportCredentials" --include="*.go"
```

## Proto File Patterns

### Service Definition

```protobuf
syntax = "proto3";

package user.v1;

option go_package = "myapp/gen/go/user/v1;userv1";

service UserService {
  // Unary RPC
  rpc GetUser(GetUserRequest) returns (GetUserResponse);

  // Server streaming
  rpc ListUsers(ListUsersRequest) returns (stream User);

  // Client streaming
  rpc CreateUsers(stream CreateUserRequest) returns (CreateUsersResponse);

  // Bidirectional streaming
  rpc Chat(stream ChatMessage) returns (stream ChatMessage);
}

message GetUserRequest {
  string id = 1;
}

message GetUserResponse {
  User user = 1;
}

message User {
  string id = 1;
  string email = 2;
  string name = 3;
  google.protobuf.Timestamp created_at = 4;
}
```

## Common Patterns

### Server Implementation

See `reference/server-patterns.md` for detailed patterns.

```go
type userServer struct {
    pb.UnimplementedUserServiceServer
    userService *service.UserService
}

func (s *userServer) GetUser(ctx context.Context, req *pb.GetUserRequest) (*pb.GetUserResponse, error) {
    user, err := s.userService.GetByID(ctx, req.GetId())
    if err != nil {
        return nil, status.Errorf(codes.NotFound, "user not found: %v", err)
    }
    return &pb.GetUserResponse{User: toProto(user)}, nil
}
```

### Interceptor Pattern

See `reference/interceptor-patterns.md` for detailed patterns.

```go
func AuthInterceptor(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo, handler grpc.UnaryHandler) (interface{}, error) {
    md, ok := metadata.FromIncomingContext(ctx)
    if !ok {
        return nil, status.Error(codes.Unauthenticated, "no metadata")
    }
    // Validate token...
    return handler(ctx, req)
}
```

### Streaming Pattern

See `reference/streaming-patterns.md` for detailed patterns.

```go
func (s *userServer) ListUsers(req *pb.ListUsersRequest, stream pb.UserService_ListUsersServer) error {
    users, err := s.userService.List(stream.Context())
    if err != nil {
        return status.Errorf(codes.Internal, "failed to list users: %v", err)
    }
    for _, user := range users {
        if err := stream.Send(toProto(user)); err != nil {
            return err
        }
    }
    return nil
}
```

## Buf Configuration

### buf.yaml

```yaml
version: v1
breaking:
  use:
    - FILE
lint:
  use:
    - DEFAULT
  except:
    - PACKAGE_VERSION_SUFFIX
```

### buf.gen.yaml

```yaml
version: v1
plugins:
  - plugin: go
    out: gen/go
    opt: paths=source_relative
  - plugin: go-grpc
    out: gen/go
    opt: paths=source_relative
  - plugin: grpc-gateway
    out: gen/go
    opt: paths=source_relative
```

## Output Format

Generate a gRPC API onboarding report with:

### 1. Service Overview
- Total services and RPCs
- Proto package structure
- Generated code locations

### 2. Service Diagram (Mermaid)

Generate `docs/grpc-services.md` with service visualization:

```markdown
# gRPC Services

## Service Map

\`\`\`mermaid
<!-- See reference/service-diagram.mmd -->
\`\`\`

## RPC Reference

| Service | RPC | Type | Request | Response |
|---------|-----|------|---------|----------|
| UserService | GetUser | Unary | GetUserRequest | GetUserResponse |
| UserService | ListUsers | Server Stream | ListUsersRequest | User (stream) |
```

### 3. Interceptor Chain
- Server interceptors (order matters)
- Client interceptors
- Purpose of each interceptor

### 4. Streaming Analysis
- Server streaming RPCs
- Client streaming RPCs
- Bidirectional streaming RPCs

### 5. Error Handling
- Status codes used
- Error detail patterns
- Retry policies

### 6. Key Files

Recommended reading order:
1. `buf.yaml` / `buf.gen.yaml` - Build configuration
2. `api/proto/**/*.proto` - Service definitions
3. `cmd/server/main.go` - Server setup
4. `internal/server/*.go` - Service implementations
5. `internal/interceptor/*.go` - Middleware

## Performance Checklist

- [ ] Connection pooling configured
- [ ] Keep-alive settings appropriate
- [ ] Interceptors ordered correctly (auth before logging)
- [ ] Streaming properly handles context cancellation
- [ ] Error codes used appropriately
- [ ] Metadata propagated correctly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/runxgalee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
