---
name: http-client-skill
description: This skill should be used when the user asks to "call HTTP API", "send HTTP request", "make API call", "handle HTTP response", "HTTP 客户端", "调用外部API", or needs to call external HTTP APIs including sending requests, handling responses, encoding conversion, CBOR encoding, error handling, and response parsing. Use when this capability is needed.
metadata:
  author: penitence1992
---

# HTTP 客户端模式

## HTTP 请求封装

```go
import "net/http"

func Post(ctx context.Context, url string, body []byte, headers ...func(r *http.Request)) ([]byte, int, error) {
    req, err := http.NewRequestWithContext(ctx, http.MethodPost, url, bytes.NewReader(body))
    if err != nil {
        return nil, 0, err
    }
    req.Header.Set("Content-Type", "application/json")

    // 自定义 Header
    for _, h := range headers {
        h(req)
    }

    client := &http.Client{Timeout: 30 * time.Second}
    resp, err := client.Do(req)
    if err != nil {
        return nil, 0, err
    }
    defer resp.Body.Close()

    respBody, _ := io.ReadAll(resp.Body)
    return respBody, resp.StatusCode, nil
}
```

## CBOR 编码请求

```go
import (
    "github.com/segmentio/encoding/cbor"
    "github.com/segmentio/encoding/json"
)

type RequestModel struct {
    Source []MatchInfo `cbor:"source" json:"source"`
    Target []MatchInfo `cbor:"target" json:"target"`
    Sport  int32       `cbor:"sport" json:"sport"`
}

// 编码为 CBOR
func (s *Job) similarRequest(ctx context.Context, req *RequestModel) ([]byte, error) {
    reqCbor, err := cbor.Marshal(req)
    if err != nil {
        return nil, err
    }

    res, _, err := Post(ctx, apiUrl, reqCbor, func(r *http.Request) {
        r.Header.Set("x-api-key", "your-api-key")
    })
    return res, err
}

// 响应解码
type ResponseModel struct {
    Code    int32  `json:"code"`
    Data    string `json:"data"`
    Message string `json:"message"`
}

var respModel ResponseModel
if err = json.Unmarshal(res, &respModel); err != nil {
    return
}
```

## 基础认证

```go
func BasicAuth(username, password string) func(r *http.Request) {
    return func(r *http.Request) {
        r.SetBasicAuth(username, password)
    }
}

// 使用
Post(ctx, url, body, BasicAuth("user", "pass"))
```

## Bearer Token

```go
func BearerToken(token string) func(r *http.Request) {
    return func(r *http.Request) {
        r.Header.Set("Authorization", "Bearer "+token)
    }
}
```

## 自定义 Header

```go
func WithHeader(key, value string) func(r *http.Request) {
    return func(r *http.Request) {
        r.Header.Set(key, value)
    }
}

// 使用
Post(ctx, url, body,
    WithHeader("X-Request-Id", "12345"),
    WithHeader("X-Tenant-Id", "tenant1"),
)
```

## 重试机制

```go
func PostWithRetry(ctx context.Context, url string, body []byte, maxRetries int) ([]byte, error) {
    var lastErr error
    for i := 0; i < maxRetries; i++ {
        resp, statusCode, err := Post(ctx, url, body)
        if err != nil {
            lastErr = err
            time.Sleep(time.Duration(i+1) * time.Second)
            continue
        }
        if statusCode >= 200 && statusCode < 300 {
            return resp, nil
        }
        lastErr = fmt.Errorf("status code: %d", statusCode)
        time.Sleep(time.Duration(i+1) * time.Second)
    }
    return nil, lastErr
}
```

## 响应处理

```go
type ApiResponse[T any] struct {
    Code    int32  `json:"code"`
    Data    T      `json:"data"`
    Message string `json:"message"`
}

func DecodeResponse[T any](respBody []byte) (*ApiResponse[T], error) {
    var resp ApiResponse[T]
    if err := json.Unmarshal(respBody, &resp); err != nil {
        return nil, err
    }
    if resp.Code != 0 {
        return nil, fmt.Errorf("api error: %s", resp.Message)
    }
    return &resp, nil
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
