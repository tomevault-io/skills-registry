---
name: golang-patterns
description: 견고하고 효율적이며 유지보수 가능한 Go 애플리케이션 구축을 위한 관용적 Go 패턴, 모범 사례 및 규칙. Use when this capability is needed.
metadata:
  author: SAM42-Lab
---

# Go 개발 패턴

견고하고 효율적이며 유지보수 가능한 애플리케이션 구축을 위한 관용적 Go 패턴과 모범 사례입니다.

## 활성화 시점

- 새로운 Go 코드를 작성할 때
- Go 코드를 리뷰할 때
- 기존 Go 코드를 리팩터링할 때
- Go 패키지/모듈을 설계할 때

## 핵심 원칙

### 1. 단순성과 명확성

Go는 기교를 부리는 것보다 단순함을 선호합니다. 코드는 명확하고 읽기 쉬워야 합니다.

```go
// 좋음: 명확하고 직접적임
func GetUser(id string) (*User, error) {
    user, err := db.FindUser(id)
    if err != nil {
        return nil, fmt.Errorf("사용자 %s 조회 실패: %w", id, err)
    }
    return user, nil
}

// 나쁨: 과하게 기교를 부림
func GetUser(id string) (*User, error) {
    return func() (*User, error) {
        if u, e := db.FindUser(id); e == nil {
            return u, nil
        } else {
            return nil, e
        }
    }()
}
```

### 2. 제로 값을 유용하게 만들기 (Make the zero value useful)

제로 값이 초기화 없이도 즉시 사용 가능하도록 타입을 설계하세요.

```go
// 좋음: 제로 값을 바로 사용할 수 있음
type Counter struct {
    mu    sync.Mutex
    count int // 제로 값은 0이며, 바로 사용 가능
}

func (c *Counter) Inc() {
    c.mu.Lock()
    c.count++
    c.mu.Unlock()
}

// 좋음: bytes.Buffer는 제로 값 상태에서 바로 작동함
var buf bytes.Buffer
buf.WriteString("hello")

// 나쁨: 명시적인 초기화가 필요함
type BadCounter struct {
    counts map[string]int // nil 맵은 패닉(panic)을 유발함
}
```

### 3. 인터페이스를 인자로 받고 구조체를 반환하기 (Accept interfaces, return structs)

함수는 인터페이스 매개변수를 받고 구체적인(concrete) 타입을 반환해야 합니다.

```go
// 좋음: 인터페이스를 받고, 구체적인 타입을 반환함
func ProcessData(r io.Reader) (*Result, error) {
    data, err := io.ReadAll(r)
    if err != nil {
        return nil, err
    }
    return &Result{Data: data}, nil
}

// 나쁨: 인터페이스를 반환함 (불필요하게 구현 세부 정보를 숨김)
func ProcessData(r io.Reader) (io.Reader, error) {
    // ...
}
```

## 에러 처리 패턴

### 컨텍스트가 포함된 에러 래핑 (Error Wrapping)

```go
// 좋음: 컨텍스트와 함께 에러를 래핑함
func LoadConfig(path string) (*Config, error) {
    data, err := os.ReadFile(path)
    if err != nil {
        return nil, fmt.Errorf("설정 파일 %s 읽기 실패: %w", path, err)
    }

    var cfg Config
    if err := json.Unmarshal(data, &cfg); err != nil {
        return nil, fmt.Errorf("설정 파일 %s 파싱 실패: %w", path, err)
    }

    return &cfg, nil
}
```

### 커스텀 에러 타입

```go
// 도메인 전용 에러 정의
type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("%s 필드 검증 실패: %s", e.Field, e.Message)
}

// 일반적인 케이스를 위한 센티널(Sentinel) 에러
var (
    ErrNotFound     = errors.New("리소스를 찾을 수 없음")
    ErrUnauthorized = errors.New("권한 없음")
    ErrInvalidInput = errors.New("유효하지 않은 입력")
)
```

### errors.Is와 errors.As를 사용한 에러 확인

```go
func HandleError(err error) {
    // 특정 에러 확인
    if errors.Is(err, sql.ErrNoRows) {
        log.Println("레코드를 찾을 수 없음")
        return
    }

    // 에러 타입 확인
    var validationErr *ValidationError
    if errors.As(err, &validationErr) {
        log.Printf("%s 필드 검증 에러: %s",
            validationErr.Field, validationErr.Message)
        return
    }

    // 알 수 없는 에러
    log.Printf("예상치 못한 에러: %v", err)
}
```

### 에러를 절대로 무시하지 말 것

```go
// 나쁨: 빈 식별자(_)로 에러를 무시함
result, _ := doSomething()

// 좋음: 에러를 처리하거나, 무시해도 안전한 이유를 명시적으로 문서화함
result, err := doSomething()
if err != nil {
    return err
}

// 허용되는 경우: 에러가 정말로 중요하지 않을 때 (드문 경우)
_ = writer.Close() // 최선을 다해 정리하지만, 에러는 다른 곳에서 기록됨
```

## 동시성 패턴 (Concurrency Patterns)

### 워커 풀 (Worker Pool)

```go
func WorkerPool(jobs <-chan Job, results chan<- Result, numWorkers int) {
    var wg sync.WaitGroup

    for i := 0; i < numWorkers; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            for job := range jobs {
                results <- process(job)
            }
        }()
    }

    wg.Wait()
    close(results)
}
```

### 취소 및 타임아웃을 위한 Context

```go
func FetchWithTimeout(ctx context.Context, url string) ([]byte, error) {
    ctx, cancel := context.WithTimeout(ctx, 5*time.Second)
    defer cancel()

    req, err := http.NewRequestWithContext(ctx, "GET", url, nil)
    if err != nil {
        return nil, fmt.Errorf("요청 생성 실패: %w", err)
    }

    resp, err := http.DefaultClient.Do(req)
    if err != nil {
        return nil, fmt.Errorf("%s 호출 실패: %w", url, err)
    }
    defer resp.Body.Close()

    return io.ReadAll(resp.Body)
}
```

### 우아한 종료 (Graceful Shutdown)

```go
func GracefulShutdown(server *http.Server) {
    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)

    <-quit
    log.Println("서버 종료 중...")

    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer cancel()

    if err := server.Shutdown(ctx); err != nil {
        log.Fatalf("서버 강제 종료: %v", err)
    }

    log.Println("서버 종료 완료")
}
```

### 여러 고루틴 조율을 위한 errgroup

```go
import "golang.org/x/sync/errgroup"

func FetchAll(ctx context.Context, urls []string) ([][]byte, error) {
    g, ctx := errgroup.WithContext(ctx)
    results := make([][]byte, len(urls))

    for i, url := range urls {
        i, url := i, url // 루프 변수 캡처
        g.Go(func() error {
            data, err := FetchWithTimeout(ctx, url)
            if err != nil {
                return err
            }
            results[i] = data
            return nil
        })
    }

    if err := g.Wait(); err != nil {
        return nil, err
    }
    return results, nil
}
```

### 고루틴 누수(Leak) 방지

```go
// 나쁨: 컨텍스트가 취소되어도 고루틴이 누수될 수 있음
func leakyFetch(ctx context.Context, url string) <-chan []byte {
    ch := make(chan []byte)
    go func() {
        data, _ := fetch(url)
        ch <- data // 받는 쪽이 없으면 영원히 블록됨
    }()
    return ch
}

// 좋음: 취소를 적절히 처리함
func safeFetch(ctx context.Context, url string) <-chan []byte {
    ch := make(chan []byte, 1) // 버퍼가 있는 채널
    go func() {
        data, err := fetch(url)
        if err != nil {
            return
        }
        select {
        case ch <- data:
        case <-ctx.Done():
        }
    }()
    return ch
}
```

## 인터페이스 설계

### 작고 집중된 인터페이스

```go
// 좋음: 단일 메서드 인터페이스
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

type Closer interface {
    Close() error
}

// 필요한 경우 인터페이스를 조합함
type ReadWriteCloser interface {
    Reader
    Writer
    Closer
}
```

### 사용하는 곳에서 인터페이스 정의

```go
// 공급자 패키지가 아닌 소비자 패키지에서 정의
package service

// UserStore는 이 서비스가 필요로 하는 기능을 정의함
type UserStore interface {
    GetUser(id string) (*User, error)
    SaveUser(user *User) error
}

type Service struct {
    store UserStore
}

// 실제 구현체는 다른 패키지에 있을 수 있음
// 구현체는 이 인터페이스에 대해 알 필요가 없음
```

### 타입 어서션(Type Assertion)을 통한 선택적 동작

```go
type Flusher interface {
    Flush() error
}

func WriteAndFlush(w io.Writer, data []byte) error {
    if _, err := w.Write(data); err != nil {
        return err
    }

    // 기능을 지원하는 경우 Flush 호출
    if f, ok := w.(Flusher); ok {
        return f.Flush()
    }
    return nil
}
```

## 패키지 구성

### 표준 프로젝트 레이아웃

```text
myproject/
├── cmd/
│   └── myapp/
│       └── main.go           # 진입점(Entry point)
├── internal/
│   ├── handler/              # HTTP 핸들러
│   ├── service/              # 비즈니스 로직
│   ├── repository/           # 데이터 액세스
│   └── config/               # 설정
├── pkg/
│   └── client/               # 공개 API 클라이언트
├── api/
│   └── v1/                   # API 정의 (proto, OpenAPI)
├── testdata/                 # 테스트 픽스처
├── go.mod
├── go.sum
└── Makefile
```

### 패키지 명명 규칙

```go
// 좋음: 짧고, 소문자이며, 언더스코어(_)가 없음
package http
package json
package user

// 나쁨: 장황하거나, 대소문자가 섞여 있거나, 중복됨
package httpHandler
package json_parser
package userService // 'Service' 접미사는 중복임
```

### 패키지 수준의 상태(State) 피하기

```go
// 나쁨: 전역 가변 상태
var db *sql.DB

func init() {
    db, _ = sql.Open("postgres", os.Getenv("DATABASE_URL"))
}

// 좋음: 의존성 주입(Dependency Injection)
type Server struct {
    db *sql.DB
}

func NewServer(db *sql.DB) *Server {
    return &Server{db: db}
}
```

## 구조체 설계

### 함수형 옵션 패턴 (Functional Options Pattern)

```go
type Server struct {
    addr    string
    timeout time.Duration
    logger  *log.Logger
}

type Option func(*Server)

func WithTimeout(d time.Duration) Option {
    return func(s *Server) {
        s.timeout = d
    }
}

func WithLogger(l *log.Logger) Option {
    return func(s *Server) {
        s.logger = l
    }
}

func NewServer(addr string, opts ...Option) *Server {
    s := &Server{
        addr:    addr,
        timeout: 30 * time.Second, // 기본값
        logger:  log.Default(),    // 기본값
    }
    for _, opt := range opts {
        opt(s)
    }
    return s
}

// 사용 예시
server := NewServer(":8080",
    WithTimeout(60*time.Second),
    WithLogger(customLogger),
)
```

### 합성을 위한 임베딩 (Embedding)

```go
type Logger struct {
    prefix string
}

func (l *Logger) Log(msg string) {
    fmt.Printf("[%s] %s\n", l.prefix, msg)
}

type Server struct {
    *Logger // 임베딩 - Server가 Log 메서드를 가짐
    addr    string
}

func NewServer(addr string) *Server {
    return &Server{
        Logger: &Logger{prefix: "SERVER"},
        addr:   addr,
    }
}

// 사용 예시
s := NewServer(":8080")
s.Log("시작 중...") // 임베딩된 Logger.Log 호출
```

## 메모리 및 성능

### 크기를 알 때 슬라이스 미리 할당 (Pre-allocation)

```go
// 나쁨: 슬라이스가 여러 번 확장됨
func processItems(items []Item) []Result {
    var results []Result
    for _, item := range items {
        results = append(results, process(item))
    }
    return results
}

// 좋음: 단일 할당
func processItems(items []Item) []Result {
    results := make([]Result, 0, len(items))
    for _, item := range items {
        results = append(results, process(item))
    }
    return results
}
```

### 빈번한 할당에 sync.Pool 사용

```go
var bufferPool = sync.Pool{
    New: func() interface{} {
        return new(bytes.Buffer)
    },
}

func ProcessRequest(data []byte) []byte {
    buf := bufferPool.Get().(*bytes.Buffer)
    defer func() {
        buf.Reset()
        bufferPool.Put(buf)
    }()

    buf.Write(data)
    // 처리...
    out := append([]byte(nil), buf.Bytes()...)
    return out
}
```

### 루프에서 문자열 연결 피하기

```go
// 나쁨: 많은 문자열 할당을 생성함
func join(parts []string) string {
    var result string
    for _, p := range parts {
        result += p + ","
    }
    return result
}

// 좋음: strings.Builder를 사용한 단일 할당
func join(parts []string) string {
    var sb strings.Builder
    for i, p := range parts {
        if i > 0 {
            sb.WriteString(",")
        }
        sb.WriteString(p)
    }
    return sb.String()
}

// 최선: 표준 라이브러리 사용
func join(parts []string) string {
    return strings.Join(parts, ",")
}
```

## Go 도구 통합

### 필수 명령어

```bash
# 빌드 및 실행
go build ./...
go run ./cmd/myapp

# 테스트
go test ./...
go test -race ./...
go test -cover ./...

# 정적 분석
go vet ./...
staticcheck ./...
golangci-lint run

# 모듈 관리
go mod tidy
go mod verify

# 포맷팅
gofmt -w .
goimports -w .
```

### 권장 린터(Linter) 구성 (.golangci.yml)

```yaml
linters:
  enable:
    - errcheck
    - gosimple
    - govet
    - ineffassign
    - staticcheck
    - unused
    - gofmt
    - goimports
    - misspell
    - unconvert
    - unparam

linters-settings:
  errcheck:
    check-type-assertions: true
  govet:
    check-shadowing: true

issues:
  exclude-use-default: false
```

## 빠른 참조: Go 관용구

| 관용구 | 설명 |
|-------|-------------|
| Accept interfaces, return structs | 함수는 인터페이스 매개변수를 받고 구체적인 타입을 반환 |
| Errors are values | 에러를 예외가 아닌 일급(first-class) 값으로 취급 |
| Don't communicate by sharing memory | 고루틴 간 조율에 채널을 사용 |
| Make the zero value useful | 타입이 명시적인 초기화 없이 작동해야 함 |
| A little copying is better than a little dependency | 불필요한 외부 의존성을 피함 |
| Clear is better than clever | 기교를 부리는 것보다 가독성을 우선 |
| gofmt is no one's favorite but everyone's friend | 항상 gofmt/goimports로 포맷팅 |
| Return early | 에러를 먼저 처리하고 정상 경로는 들여쓰기 없이 유지 |

## 피해야 할 안티패턴

```go
// 나쁨: 긴 함수에서 반환값 없는 반환 (Naked returns)
func process() (result int, err error) {
    // ... 50 라인 ...
    return // 무엇이 반환되는지 알기 어려움
}

// 나쁨: 제어 흐름에 패닉(panic) 사용
func GetUser(id string) *User {
    user, err := db.Find(id)
    if err != nil {
        panic(err) // 이렇게 하지 마세요
    }
    return user
}

// 나쁨: 구조체에 컨텍스트 전달
type Request struct {
    ctx context.Context // 컨텍스트는 첫 번째 매개변수여야 함
    ID  string
}

// 좋음: 컨텍스트를 첫 번째 매개변수로 사용
func ProcessRequest(ctx context.Context, id string) error {
    // ...
}

// 나쁨: 값 리시버와 포인터 리시버 혼용
type Counter struct{ n int }
func (c Counter) Value() int { return c.n }    // 값 리시버
func (c *Counter) Increment() { c.n++ }        // 포인터 리시버
// 하나의 스타일을 선택하고 일관성을 유지하세요.
```

**기억하세요**: Go 코드는 최고의 의미에서 "지루해야" 합니다. 즉 예측 가능하고, 일관적이며, 이해하기 쉬워야 합니다. 의심스러울 때는 단순하게 유지하세요.

---
> Source: [SAM42-Lab/everything-claude-code-kr](https://github.com/SAM42-Lab/everything-claude-code-kr) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
