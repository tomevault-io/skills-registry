---
name: backend-patterns
description: C++ backend architecture patterns, CMake build system, vcpkg package management, and server-side best practices. Use when this capability is needed.
metadata:
  author: steven715
---

# C++ Backend Development Patterns

Backend architecture patterns and best practices for scalable C++ server-side applications.

## CMake Build System

### Basic CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.14)
project(my_project VERSION 1.0.0 LANGUAGES CXX)

# C++14 standard
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Export compile commands for IDE support
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Find vcpkg packages
find_package(fmt CONFIG REQUIRED)
find_package(spdlog CONFIG REQUIRED)
find_package(nlohmann_json CONFIG REQUIRED)

# Main library
add_library(${PROJECT_NAME}_lib
    src/core/application.cpp
    src/utils/string_utils.cpp
)

target_include_directories(${PROJECT_NAME}_lib
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
)

target_link_libraries(${PROJECT_NAME}_lib
    PUBLIC
        fmt::fmt
        spdlog::spdlog
        nlohmann_json::nlohmann_json
)

# Main executable
add_executable(${PROJECT_NAME} src/main.cpp)
target_link_libraries(${PROJECT_NAME} PRIVATE ${PROJECT_NAME}_lib)

# Enable testing
enable_testing()
add_subdirectory(tests)
```

### CMake with vcpkg Integration

```cmake
# CMakePresets.json for vcpkg integration
{
    "version": 3,
    "configurePresets": [
        {
            "name": "vcpkg",
            "hidden": true,
            "toolchainFile": "$env{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"
        },
        {
            "name": "debug",
            "inherits": "vcpkg",
            "binaryDir": "${sourceDir}/build/debug",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug"
            }
        },
        {
            "name": "release",
            "inherits": "vcpkg",
            "binaryDir": "${sourceDir}/build/release",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Release"
            }
        }
    ]
}
```

### Modular CMake Structure

```cmake
# Root CMakeLists.txt
cmake_minimum_required(VERSION 3.14)
project(my_project)

# Global settings
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Options
option(BUILD_TESTS "Build tests" ON)
option(BUILD_EXAMPLES "Build examples" OFF)

# Add subdirectories
add_subdirectory(src)

if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()

if(BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()
```

## vcpkg Package Management

### vcpkg.json Manifest

```json
{
    "name": "my-project",
    "version": "1.0.0",
    "dependencies": [
        "fmt",
        "spdlog",
        "nlohmann-json",
        "boost-asio",
        "openssl",
        {
            "name": "gtest",
            "host": true
        }
    ],
    "builtin-baseline": "2024.01.12"
}
```

### Common vcpkg Commands

```bash
# Install dependencies from manifest
vcpkg install

# Install specific package
vcpkg install fmt:x64-windows

# Search for packages
vcpkg search json

# List installed packages
vcpkg list

# Update packages
vcpkg upgrade --no-dry-run

# Integrate with CMake
vcpkg integrate install
```

## Design Patterns

### Repository Pattern

```cpp
// Abstract data access logic
template<typename T, typename ID>
class IRepository {
public:
    virtual ~IRepository() = default;

    virtual std::vector<T> find_all() = 0;
    virtual std::unique_ptr<T> find_by_id(const ID& id) = 0;
    virtual void create(const T& entity) = 0;
    virtual void update(const T& entity) = 0;
    virtual void remove(const ID& id) = 0;
};

// Concrete implementation
class UserRepository : public IRepository<User, int> {
public:
    explicit UserRepository(Database& db) : m_db(db) {}

    std::vector<User> find_all() override {
        auto result = m_db.query("SELECT * FROM users");
        std::vector<User> users;
        users.reserve(result.size());

        for (const auto& row : result) {
            users.emplace_back(
                row.get<int>("id"),
                row.get<std::string>("name"),
                row.get<std::string>("email")
            );
        }
        return users;
    }

    std::unique_ptr<User> find_by_id(const int& id) override {
        auto result = m_db.query(
            "SELECT * FROM users WHERE id = ?", id
        );

        if (result.empty()) {
            return nullptr;
        }

        return std::make_unique<User>(
            result[0].get<int>("id"),
            result[0].get<std::string>("name"),
            result[0].get<std::string>("email")
        );
    }

    // Other methods...

private:
    Database& m_db;
};
```

### Service Layer Pattern

```cpp
// Business logic separated from data access
class UserService {
public:
    explicit UserService(std::unique_ptr<IRepository<User, int>> repo)
        : m_repo(std::move(repo)) {}

    User create_user(const std::string& name, const std::string& email) {
        // Validate input
        if (name.empty()) {
            throw std::invalid_argument("Name cannot be empty");
        }

        if (!is_valid_email(email)) {
            throw std::invalid_argument("Invalid email format");
        }

        // Check for duplicates
        auto existing = find_by_email(email);
        if (existing) {
            throw std::runtime_error("User with this email already exists");
        }

        // Create user
        User user{0, name, email};
        m_repo->create(user);

        return user;
    }

    std::unique_ptr<User> find_by_id(int id) {
        return m_repo->find_by_id(id);
    }

private:
    std::unique_ptr<IRepository<User, int>> m_repo;

    bool is_valid_email(const std::string& email) {
        // Simple validation
        return email.find('@') != std::string::npos;
    }
};
```

### Dependency Injection

```cpp
// Interface
class ILogger {
public:
    virtual ~ILogger() = default;
    virtual void log(const std::string& message) = 0;
};

// Concrete implementation
class ConsoleLogger : public ILogger {
public:
    void log(const std::string& message) override {
        std::cout << "[LOG] " << message << std::endl;
    }
};

class FileLogger : public ILogger {
public:
    explicit FileLogger(const std::string& path)
        : m_file(path, std::ios::app) {}

    void log(const std::string& message) override {
        m_file << "[LOG] " << message << std::endl;
    }

private:
    std::ofstream m_file;
};

// Service using dependency injection
class PaymentService {
public:
    explicit PaymentService(std::shared_ptr<ILogger> logger)
        : m_logger(std::move(logger)) {}

    void process_payment(double amount) {
        m_logger->log("Processing payment: " + std::to_string(amount));
        // Process payment...
        m_logger->log("Payment completed");
    }

private:
    std::shared_ptr<ILogger> m_logger;
};

// Usage
auto logger = std::make_shared<ConsoleLogger>();
PaymentService service(logger);
service.process_payment(100.0);
```

### Factory Pattern

```cpp
// Product interface
class IConnection {
public:
    virtual ~IConnection() = default;
    virtual void connect() = 0;
    virtual void disconnect() = 0;
    virtual bool is_connected() const = 0;
};

// Concrete products
class PostgresConnection : public IConnection {
public:
    void connect() override { /* ... */ }
    void disconnect() override { /* ... */ }
    bool is_connected() const override { return m_connected; }
private:
    bool m_connected = false;
};

class MySqlConnection : public IConnection {
public:
    void connect() override { /* ... */ }
    void disconnect() override { /* ... */ }
    bool is_connected() const override { return m_connected; }
private:
    bool m_connected = false;
};

// Factory
class ConnectionFactory {
public:
    enum class DatabaseType { Postgres, MySql };

    static std::unique_ptr<IConnection> create(DatabaseType type) {
        switch (type) {
            case DatabaseType::Postgres:
                return std::make_unique<PostgresConnection>();
            case DatabaseType::MySql:
                return std::make_unique<MySqlConnection>();
            default:
                throw std::invalid_argument("Unknown database type");
        }
    }
};

// Usage
auto conn = ConnectionFactory::create(ConnectionFactory::DatabaseType::Postgres);
conn->connect();
```

## HTTP Server Patterns

### Using Boost.Beast (HTTP Server)

```cpp
#include <boost/beast/core.hpp>
#include <boost/beast/http.hpp>
#include <boost/asio.hpp>

namespace beast = boost::beast;
namespace http = beast::http;
namespace net = boost::asio;
using tcp = net::ip::tcp;

class HttpSession : public std::enable_shared_from_this<HttpSession> {
public:
    explicit HttpSession(tcp::socket socket)
        : m_socket(std::move(socket)) {}

    void start() {
        read_request();
    }

private:
    void read_request() {
        auto self = shared_from_this();

        http::async_read(
            m_socket, m_buffer, m_request,
            [self](beast::error_code ec, std::size_t) {
                if (!ec) {
                    self->handle_request();
                }
            });
    }

    void handle_request() {
        // Route request
        if (m_request.target() == "/api/users" &&
            m_request.method() == http::verb::get) {
            handle_get_users();
        } else if (m_request.target() == "/api/health") {
            handle_health_check();
        } else {
            send_not_found();
        }
    }

    void handle_get_users() {
        http::response<http::string_body> response{
            http::status::ok, m_request.version()
        };
        response.set(http::field::content_type, "application/json");
        response.body() = R"({"users": []})";
        response.prepare_payload();

        send_response(std::move(response));
    }

    void handle_health_check() {
        http::response<http::string_body> response{
            http::status::ok, m_request.version()
        };
        response.set(http::field::content_type, "application/json");
        response.body() = R"({"status": "healthy"})";
        response.prepare_payload();

        send_response(std::move(response));
    }

    void send_not_found() {
        http::response<http::string_body> response{
            http::status::not_found, m_request.version()
        };
        response.body() = "Not Found";
        response.prepare_payload();

        send_response(std::move(response));
    }

    template<typename Body>
    void send_response(http::response<Body> response) {
        auto sp = std::make_shared<http::response<Body>>(std::move(response));
        auto self = shared_from_this();

        http::async_write(
            m_socket, *sp,
            [self, sp](beast::error_code ec, std::size_t) {
                self->m_socket.shutdown(tcp::socket::shutdown_send, ec);
            });
    }

    tcp::socket m_socket;
    beast::flat_buffer m_buffer;
    http::request<http::string_body> m_request;
};
```

### REST API Router

```cpp
#include <functional>
#include <unordered_map>
#include <regex>

class Router {
public:
    using Handler = std::function<Response(const Request&)>;

    void get(const std::string& path, Handler handler) {
        m_routes["GET"][path] = std::move(handler);
    }

    void post(const std::string& path, Handler handler) {
        m_routes["POST"][path] = std::move(handler);
    }

    void put(const std::string& path, Handler handler) {
        m_routes["PUT"][path] = std::move(handler);
    }

    void del(const std::string& path, Handler handler) {
        m_routes["DELETE"][path] = std::move(handler);
    }

    Response route(const Request& request) {
        auto method = request.method();
        auto path = request.path();

        auto method_it = m_routes.find(method);
        if (method_it == m_routes.end()) {
            return Response::method_not_allowed();
        }

        auto route_it = method_it->second.find(path);
        if (route_it == method_it->second.end()) {
            return Response::not_found();
        }

        return route_it->second(request);
    }

private:
    std::unordered_map<std::string,
        std::unordered_map<std::string, Handler>> m_routes;
};

// Usage
Router router;

router.get("/api/users", [](const Request& req) {
    auto users = user_service.get_all();
    return Response::json(users);
});

router.get("/api/users/:id", [](const Request& req) {
    int id = req.param<int>("id");
    auto user = user_service.find_by_id(id);
    if (!user) {
        return Response::not_found();
    }
    return Response::json(*user);
});

router.post("/api/users", [](const Request& req) {
    auto body = req.json();
    auto user = user_service.create(body);
    return Response::created(user);
});
```

## Database Patterns

### Connection Pool

```cpp
#include <mutex>
#include <condition_variable>
#include <queue>

template<typename Connection>
class ConnectionPool {
public:
    explicit ConnectionPool(size_t pool_size, ConnectionFactory factory)
        : m_factory(std::move(factory))
        , m_max_size(pool_size) {

        for (size_t i = 0; i < pool_size; ++i) {
            m_pool.push(m_factory());
        }
    }

    class ConnectionGuard {
    public:
        ConnectionGuard(ConnectionPool& pool, std::unique_ptr<Connection> conn)
            : m_pool(pool), m_conn(std::move(conn)) {}

        ~ConnectionGuard() {
            m_pool.release(std::move(m_conn));
        }

        Connection* operator->() { return m_conn.get(); }
        Connection& operator*() { return *m_conn; }

    private:
        ConnectionPool& m_pool;
        std::unique_ptr<Connection> m_conn;
    };

    ConnectionGuard acquire() {
        std::unique_lock<std::mutex> lock(m_mutex);

        m_cv.wait(lock, [this] { return !m_pool.empty(); });

        auto conn = std::move(m_pool.front());
        m_pool.pop();

        return ConnectionGuard(*this, std::move(conn));
    }

private:
    void release(std::unique_ptr<Connection> conn) {
        std::lock_guard<std::mutex> lock(m_mutex);
        m_pool.push(std::move(conn));
        m_cv.notify_one();
    }

    std::function<std::unique_ptr<Connection>()> m_factory;
    std::queue<std::unique_ptr<Connection>> m_pool;
    std::mutex m_mutex;
    std::condition_variable m_cv;
    size_t m_max_size;
};

// Usage
ConnectionPool<DbConnection> pool(10, []() {
    return std::make_unique<DbConnection>("localhost", 5432);
});

void handle_request() {
    auto conn = pool.acquire();
    conn->execute("SELECT * FROM users");
    // Connection automatically returned to pool when guard goes out of scope
}
```

### Transaction Pattern

```cpp
class Transaction {
public:
    explicit Transaction(Connection& conn)
        : m_conn(conn)
        , m_committed(false) {
        m_conn.execute("BEGIN");
    }

    ~Transaction() {
        if (!m_committed) {
            try {
                m_conn.execute("ROLLBACK");
            } catch (...) {
                // Log error, but don't throw from destructor
            }
        }
    }

    void commit() {
        m_conn.execute("COMMIT");
        m_committed = true;
    }

    Connection& connection() { return m_conn; }

private:
    Connection& m_conn;
    bool m_committed;
};

// Usage
void transfer_funds(int from_id, int to_id, double amount) {
    auto conn = pool.acquire();
    Transaction tx(*conn);

    conn->execute("UPDATE accounts SET balance = balance - ? WHERE id = ?",
                  amount, from_id);
    conn->execute("UPDATE accounts SET balance = balance + ? WHERE id = ?",
                  amount, to_id);

    tx.commit();  // Only if both succeed
    // If exception thrown, destructor will ROLLBACK
}
```

## Caching Strategies

### LRU Cache

```cpp
#include <list>
#include <unordered_map>
#include <mutex>

template<typename Key, typename Value>
class LRUCache {
public:
    explicit LRUCache(size_t capacity) : m_capacity(capacity) {}

    std::unique_ptr<Value> get(const Key& key) {
        std::lock_guard<std::mutex> lock(m_mutex);

        auto it = m_cache.find(key);
        if (it == m_cache.end()) {
            return nullptr;
        }

        // Move to front (most recently used)
        m_lru.splice(m_lru.begin(), m_lru, it->second.second);

        return std::make_unique<Value>(it->second.first);
    }

    void put(const Key& key, const Value& value) {
        std::lock_guard<std::mutex> lock(m_mutex);

        auto it = m_cache.find(key);
        if (it != m_cache.end()) {
            // Update existing
            it->second.first = value;
            m_lru.splice(m_lru.begin(), m_lru, it->second.second);
            return;
        }

        // Evict if full
        if (m_cache.size() >= m_capacity) {
            auto last = m_lru.back();
            m_cache.erase(last);
            m_lru.pop_back();
        }

        // Insert new
        m_lru.push_front(key);
        m_cache[key] = {value, m_lru.begin()};
    }

    void remove(const Key& key) {
        std::lock_guard<std::mutex> lock(m_mutex);

        auto it = m_cache.find(key);
        if (it != m_cache.end()) {
            m_lru.erase(it->second.second);
            m_cache.erase(it);
        }
    }

private:
    size_t m_capacity;
    std::list<Key> m_lru;
    std::unordered_map<Key, std::pair<Value, typename std::list<Key>::iterator>> m_cache;
    std::mutex m_mutex;
};

// Usage
LRUCache<int, User> user_cache(1000);

std::unique_ptr<User> get_user(int id) {
    // Try cache first
    auto cached = user_cache.get(id);
    if (cached) {
        return cached;
    }

    // Cache miss - fetch from database
    auto user = db.find_user(id);
    if (user) {
        user_cache.put(id, *user);
    }

    return user;
}
```

## Error Handling Patterns

### Result Type

```cpp
template<typename T, typename E = std::string>
class Result {
public:
    static Result ok(T value) {
        return Result(std::move(value));
    }

    static Result err(E error) {
        return Result(std::move(error), false);
    }

    bool is_ok() const { return m_is_ok; }
    bool is_err() const { return !m_is_ok; }

    T& value() {
        if (!m_is_ok) throw std::runtime_error("Result is error");
        return m_value;
    }

    const T& value() const {
        if (!m_is_ok) throw std::runtime_error("Result is error");
        return m_value;
    }

    E& error() {
        if (m_is_ok) throw std::runtime_error("Result is ok");
        return m_error;
    }

    template<typename F>
    auto map(F&& f) -> Result<decltype(f(m_value)), E> {
        if (m_is_ok) {
            return Result<decltype(f(m_value)), E>::ok(f(m_value));
        }
        return Result<decltype(f(m_value)), E>::err(m_error);
    }

private:
    explicit Result(T value) : m_value(std::move(value)), m_is_ok(true) {}
    Result(E error, bool) : m_error(std::move(error)), m_is_ok(false) {}

    T m_value;
    E m_error;
    bool m_is_ok;
};

// Usage
Result<User, std::string> find_user(int id) {
    auto user = db.find(id);
    if (!user) {
        return Result<User, std::string>::err("User not found");
    }
    return Result<User, std::string>::ok(*user);
}

void handle_request(int user_id) {
    auto result = find_user(user_id);

    if (result.is_err()) {
        send_error(404, result.error());
        return;
    }

    send_json(result.value());
}
```

### Retry with Exponential Backoff

```cpp
#include <chrono>
#include <thread>
#include <random>

template<typename F>
auto retry_with_backoff(F&& func, int max_retries = 3) -> decltype(func()) {
    std::random_device rd;
    std::mt19937 gen(rd());

    for (int attempt = 0; attempt < max_retries; ++attempt) {
        try {
            return func();
        } catch (const std::exception& e) {
            if (attempt == max_retries - 1) {
                throw;  // Last attempt, rethrow
            }

            // Exponential backoff with jitter
            int base_delay = 1000 * (1 << attempt);  // 1s, 2s, 4s
            std::uniform_int_distribution<> dist(0, base_delay / 2);
            int delay = base_delay + dist(gen);

            std::this_thread::sleep_for(std::chrono::milliseconds(delay));
        }
    }

    throw std::runtime_error("Should not reach here");
}

// Usage
auto data = retry_with_backoff([]() {
    return api_client.fetch_data();
});
```

## Logging Patterns

### Structured Logging with spdlog

```cpp
#include <spdlog/spdlog.h>
#include <spdlog/sinks/rotating_file_sink.h>
#include <spdlog/sinks/stdout_color_sinks.h>

class Logger {
public:
    static void init(const std::string& app_name) {
        auto console_sink = std::make_shared<spdlog::sinks::stdout_color_sink_mt>();
        console_sink->set_level(spdlog::level::debug);

        auto file_sink = std::make_shared<spdlog::sinks::rotating_file_sink_mt>(
            "logs/" + app_name + ".log", 1024 * 1024 * 5, 3
        );
        file_sink->set_level(spdlog::level::info);

        std::vector<spdlog::sink_ptr> sinks{console_sink, file_sink};
        auto logger = std::make_shared<spdlog::logger>(app_name, sinks.begin(), sinks.end());

        spdlog::set_default_logger(logger);
        spdlog::set_pattern("[%Y-%m-%d %H:%M:%S.%e] [%^%l%$] [%t] %v");
    }

    static void info(const std::string& message) {
        spdlog::info(message);
    }

    static void error(const std::string& message, const std::exception& e) {
        spdlog::error("{}: {}", message, e.what());
    }

    template<typename... Args>
    static void info(const std::string& fmt, Args&&... args) {
        spdlog::info(fmt, std::forward<Args>(args)...);
    }
};

// Usage
Logger::init("my_app");
Logger::info("Server started on port {}", 8080);
Logger::info("Processing request from user {}", user_id);
```

## Configuration Management

### Config Loading with JSON

```cpp
#include <nlohmann/json.hpp>
#include <fstream>

class Config {
public:
    static Config& instance() {
        static Config config;
        return config;
    }

    void load(const std::string& path) {
        std::ifstream file(path);
        if (!file.is_open()) {
            throw std::runtime_error("Cannot open config file: " + path);
        }

        m_config = nlohmann::json::parse(file);
    }

    template<typename T>
    T get(const std::string& key) const {
        return m_config.at(key).get<T>();
    }

    template<typename T>
    T get(const std::string& key, const T& default_value) const {
        if (m_config.contains(key)) {
            return m_config.at(key).get<T>();
        }
        return default_value;
    }

    std::string get_string(const std::string& key) const {
        return get<std::string>(key);
    }

    int get_int(const std::string& key) const {
        return get<int>(key);
    }

private:
    Config() = default;
    nlohmann::json m_config;
};

// config.json
// {
//     "server": {
//         "port": 8080,
//         "host": "0.0.0.0"
//     },
//     "database": {
//         "url": "postgresql://localhost/mydb",
//         "pool_size": 10
//     }
// }

// Usage
Config::instance().load("config.json");
int port = Config::instance().get<int>("server/port");
```

## Thread Safety Patterns

### Thread-Safe Singleton

```cpp
class DatabaseManager {
public:
    static DatabaseManager& instance() {
        static DatabaseManager instance;  // C++11 thread-safe
        return instance;
    }

    DatabaseManager(const DatabaseManager&) = delete;
    DatabaseManager& operator=(const DatabaseManager&) = delete;

    void execute(const std::string& query) {
        std::lock_guard<std::mutex> lock(m_mutex);
        // Execute query...
    }

private:
    DatabaseManager() = default;
    std::mutex m_mutex;
};
```

### Producer-Consumer Queue

```cpp
template<typename T>
class ThreadSafeQueue {
public:
    void push(T item) {
        std::lock_guard<std::mutex> lock(m_mutex);
        m_queue.push(std::move(item));
        m_cv.notify_one();
    }

    T pop() {
        std::unique_lock<std::mutex> lock(m_mutex);
        m_cv.wait(lock, [this] { return !m_queue.empty(); });

        T item = std::move(m_queue.front());
        m_queue.pop();
        return item;
    }

    bool try_pop(T& item, std::chrono::milliseconds timeout) {
        std::unique_lock<std::mutex> lock(m_mutex);

        if (!m_cv.wait_for(lock, timeout, [this] { return !m_queue.empty(); })) {
            return false;
        }

        item = std::move(m_queue.front());
        m_queue.pop();
        return true;
    }

private:
    std::queue<T> m_queue;
    std::mutex m_mutex;
    std::condition_variable m_cv;
};

// Usage
ThreadSafeQueue<Task> task_queue;

// Producer
task_queue.push(Task{/* ... */});

// Consumer
while (running) {
    auto task = task_queue.pop();
    process(task);
}
```

**Remember**: Backend patterns enable scalable, maintainable server-side applications. Choose patterns that fit your complexity level.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/steven715) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
