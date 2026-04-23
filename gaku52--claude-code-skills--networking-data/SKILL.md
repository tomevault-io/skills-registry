---
name: networking-data
description: ネットワーク通信・データ永続化ガイド。API通信、HTTP/WebSocket、データキャッシュ、Core Data、Realm、UserDefaults、Keychain等、iOS開発におけるデータ取得・保存の実践的なパターン。 Use when this capability is needed.
metadata:
  author: gaku52
---

# Networking & Data Persistence Skill

## 📋 目次

1. [概要](#概要)
2. [ネットワーク通信](#ネットワーク通信)
3. [API通信パターン](#api通信パターン)
4. [WebSocket通信](#websocket通信)
5. [データ永続化](#データ永続化)
6. [Core Data](#core-data)
7. [キャッシュ戦略](#キャッシュ戦略)
8. [セキュアストレージ](#セキュアストレージ)
9. [オフライン対応](#オフライン対応)
10. [よくある問題と解決策](#よくある問題と解決策)

## 概要

iOS開発におけるネットワーク通信とデータ永続化の実践的なパターンとベストプラクティスを提供します。

**対象:**
- iOSエンジニア
- モバイルアプリ開発者
- バックエンドエンジニア（API設計者）

**このSkillでできること:**
- 型安全なAPI通信の実装
- 効率的なデータキャッシュ戦略の構築
- オフライン対応アプリの開発
- セキュアなデータ保存

## 📚 公式ドキュメント・参考リソース

**このガイドで学べること**: URLSession活用、API通信パターン、データ永続化戦略、キャッシュ実装、オフライン対応
**公式で確認すべきこと**: 最新のネットワークAPI、Core Dataアップデート、パフォーマンス最適化

### 主要な公式ドキュメント

- **[URLSession Documentation](https://developer.apple.com/documentation/foundation/urlsession)** - ネットワーク通信の基盤
  - [URLSessionTask](https://developer.apple.com/documentation/foundation/urlsessiontask)
  - [URLSessionConfiguration](https://developer.apple.com/documentation/foundation/urlsessionconfiguration)

- **[Core Data Documentation](https://developer.apple.com/documentation/coredata)** - Apple公式データ永続化フレームワーク
  - [NSPersistentContainer](https://developer.apple.com/documentation/coredata/nspersistentcontainer)
  - [Core Data Best Practices](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreData/index.html)

- **[Combine Framework](https://developer.apple.com/documentation/combine)** - 非同期処理とデータストリーム

- **[Network Framework](https://developer.apple.com/documentation/network)** - 低レベルネットワーク操作

### 関連リソース

- **[Alamofire Documentation](https://github.com/Alamofire/Alamofire)** - 人気のSwiftネットワークライブラリ
- **[Realm Documentation](https://www.mongodb.com/docs/atlas/device-sdks/)** - モバイル向けデータベース
- **[REST API Best Practices](https://restfulapi.net/)** - RESTful API設計ガイド

---

## ネットワーク通信

### URLSession基礎

**基本的なGETリクエスト:**

```swift
struct User: Codable {
    let id: Int
    let name: String
    let email: String
}

func fetchUser(id: Int) async throws -> User {
    let url = URL(string: "https://api.example.com/users/\(id)")!
    let (data, response) = try await URLSession.shared.data(from: url)

    guard let httpResponse = response as? HTTPURLResponse,
          (200...299).contains(httpResponse.statusCode) else {
        throw NetworkError.invalidResponse
    }

    return try JSONDecoder().decode(User.self, from: data)
}
```

**POSTリクエスト:**

```swift
struct CreateUserRequest: Codable {
    let name: String
    let email: String
}

func createUser(request: CreateUserRequest) async throws -> User {
    let url = URL(string: "https://api.example.com/users")!
    var urlRequest = URLRequest(url: url)
    urlRequest.httpMethod = "POST"
    urlRequest.setValue("application/json", forHTTPHeaderField: "Content-Type")
    urlRequest.httpBody = try JSONEncoder().encode(request)

    let (data, response) = try await URLSession.shared.data(for: urlRequest)

    guard let httpResponse = response as? HTTPURLResponse,
          (200...299).contains(httpResponse.statusCode) else {
        throw NetworkError.invalidResponse
    }

    return try JSONDecoder().decode(User.self, from: data)
}
```

### カスタムURLSessionの構成

```swift
class NetworkManager {
    static let shared = NetworkManager()

    private let session: URLSession

    private init() {
        let configuration = URLSessionConfiguration.default
        configuration.timeoutIntervalForRequest = 30
        configuration.timeoutIntervalForResource = 300
        configuration.waitsForConnectivity = true
        configuration.requestCachePolicy = .reloadIgnoringLocalCacheData

        self.session = URLSession(configuration: configuration)
    }

    func request<T: Decodable>(_ endpoint: Endpoint) async throws -> T {
        let request = try endpoint.makeRequest()
        let (data, response) = try await session.data(for: request)

        guard let httpResponse = response as? HTTPURLResponse else {
            throw NetworkError.invalidResponse
        }

        guard (200...299).contains(httpResponse.statusCode) else {
            throw NetworkError.httpError(httpResponse.statusCode)
        }

        return try JSONDecoder().decode(T.self, from: data)
    }
}
```

## API通信パターン

### Endpointパターン

```swift
protocol Endpoint {
    var baseURL: String { get }
    var path: String { get }
    var method: HTTPMethod { get }
    var headers: [String: String]? { get }
    var parameters: [String: Any]? { get }

    func makeRequest() throws -> URLRequest
}

enum HTTPMethod: String {
    case get = "GET"
    case post = "POST"
    case put = "PUT"
    case delete = "DELETE"
    case patch = "PATCH"
}

extension Endpoint {
    var baseURL: String { "https://api.example.com" }
    var headers: [String: String]? { ["Content-Type": "application/json"] }

    func makeRequest() throws -> URLRequest {
        guard let url = URL(string: baseURL + path) else {
            throw NetworkError.invalidURL
        }

        var request = URLRequest(url: url)
        request.httpMethod = method.rawValue

        headers?.forEach { key, value in
            request.setValue(value, forHTTPHeaderField: key)
        }

        if let parameters = parameters {
            request.httpBody = try JSONSerialization.data(withJSONObject: parameters)
        }

        return request
    }
}

// 使用例
enum UserEndpoint: Endpoint {
    case getUser(id: Int)
    case createUser(CreateUserRequest)
    case updateUser(id: Int, UpdateUserRequest)
    case deleteUser(id: Int)

    var path: String {
        switch self {
        case .getUser(let id), .deleteUser(let id):
            return "/users/\(id)"
        case .createUser, .updateUser:
            return "/users"
        }
    }

    var method: HTTPMethod {
        switch self {
        case .getUser:
            return .get
        case .createUser:
            return .post
        case .updateUser:
            return .put
        case .deleteUser:
            return .delete
        }
    }

    var parameters: [String: Any]? {
        switch self {
        case .createUser(let request):
            return try? request.asDictionary()
        case .updateUser(_, let request):
            return try? request.asDictionary()
        default:
            return nil
        }
    }
}
```

### Repositoryパターン

```swift
protocol UserRepository {
    func fetchUser(id: Int) async throws -> User
    func createUser(_ request: CreateUserRequest) async throws -> User
    func updateUser(id: Int, _ request: UpdateUserRequest) async throws -> User
    func deleteUser(id: Int) async throws
}

class UserRepositoryImpl: UserRepository {
    private let networkManager: NetworkManager

    init(networkManager: NetworkManager = .shared) {
        self.networkManager = networkManager
    }

    func fetchUser(id: Int) async throws -> User {
        try await networkManager.request(UserEndpoint.getUser(id: id))
    }

    func createUser(_ request: CreateUserRequest) async throws -> User {
        try await networkManager.request(UserEndpoint.createUser(request))
    }

    func updateUser(id: Int, _ request: UpdateUserRequest) async throws -> User {
        try await networkManager.request(UserEndpoint.updateUser(id: id, request))
    }

    func deleteUser(id: Int) async throws {
        let _: EmptyResponse = try await networkManager.request(UserEndpoint.deleteUser(id: id))
    }
}

struct EmptyResponse: Codable {}
```

### エラーハンドリング

```swift
enum NetworkError: Error, LocalizedError {
    case invalidURL
    case invalidResponse
    case httpError(Int)
    case decodingError(Error)
    case encodingError(Error)
    case noData
    case networkUnavailable

    var errorDescription: String? {
        switch self {
        case .invalidURL:
            return "Invalid URL"
        case .invalidResponse:
            return "Invalid response from server"
        case .httpError(let statusCode):
            return "HTTP error: \(statusCode)"
        case .decodingError(let error):
            return "Failed to decode response: \(error.localizedDescription)"
        case .encodingError(let error):
            return "Failed to encode request: \(error.localizedDescription)"
        case .noData:
            return "No data received"
        case .networkUnavailable:
            return "Network unavailable"
        }
    }
}

// エラーハンドリングの実装
func handleNetworkError(_ error: Error) {
    if let networkError = error as? NetworkError {
        switch networkError {
        case .httpError(401):
            // 認証エラー - ログイン画面へ遷移
            NotificationCenter.default.post(name: .unauthorized, object: nil)
        case .httpError(500...599):
            // サーバーエラー - リトライロジック
            Task { try? await retryRequest() }
        case .networkUnavailable:
            // ネットワーク不可 - オフラインモード
            enableOfflineMode()
        default:
            // その他のエラー - ユーザーに通知
            showErrorAlert(networkError.localizedDescription)
        }
    }
}
```

## WebSocket通信

### URLSessionWebSocketTask

```swift
class WebSocketManager: ObservableObject {
    @Published var isConnected = false
    @Published var messages: [String] = []

    private var webSocketTask: URLSessionWebSocketTask?
    private let url = URL(string: "wss://api.example.com/ws")!

    func connect() {
        let session = URLSession(configuration: .default)
        webSocketTask = session.webSocketTask(with: url)
        webSocketTask?.resume()
        isConnected = true

        receiveMessage()
    }

    func disconnect() {
        webSocketTask?.cancel(with: .goingAway, reason: nil)
        isConnected = false
    }

    func send(_ message: String) {
        let message = URLSessionWebSocketTask.Message.string(message)
        webSocketTask?.send(message) { error in
            if let error = error {
                print("WebSocket send error: \(error)")
            }
        }
    }

    private func receiveMessage() {
        webSocketTask?.receive { [weak self] result in
            switch result {
            case .success(let message):
                switch message {
                case .string(let text):
                    DispatchQueue.main.async {
                        self?.messages.append(text)
                    }
                case .data(let data):
                    print("Received data: \(data)")
                @unknown default:
                    break
                }
                // 次のメッセージを受信
                self?.receiveMessage()

            case .failure(let error):
                print("WebSocket receive error: \(error)")
            }
        }
    }

    func ping() {
        webSocketTask?.sendPing { error in
            if let error = error {
                print("Ping failed: \(error)")
            }
        }
    }
}
```

### リアルタイムチャット実装例

```swift
struct ChatMessage: Codable, Identifiable {
    let id: String
    let userId: String
    let text: String
    let timestamp: Date
}

class ChatViewModel: ObservableObject {
    @Published var messages: [ChatMessage] = []
    @Published var isConnected = false

    private let webSocket: WebSocketManager
    private let decoder = JSONDecoder()
    private let encoder = JSONEncoder()

    init() {
        self.webSocket = WebSocketManager()
        setupObservers()
    }

    func connect() {
        webSocket.connect()
    }

    func disconnect() {
        webSocket.disconnect()
    }

    func sendMessage(_ text: String) {
        let message = ChatMessage(
            id: UUID().uuidString,
            userId: currentUserId,
            text: text,
            timestamp: Date()
        )

        if let data = try? encoder.encode(message),
           let jsonString = String(data: data, encoding: .utf8) {
            webSocket.send(jsonString)
        }
    }

    private func setupObservers() {
        webSocket.$messages
            .compactMap { [weak self] jsonString -> ChatMessage? in
                guard let data = jsonString.data(using: .utf8) else { return nil }
                return try? self?.decoder.decode(ChatMessage.self, from: data)
            }
            .assign(to: &$messages)

        webSocket.$isConnected
            .assign(to: &$isConnected)
    }
}
```

## データ永続化

### UserDefaults

**基本的な使用:**

```swift
class UserSettings {
    private let defaults = UserDefaults.standard

    var notificationsEnabled: Bool {
        get { defaults.bool(forKey: "notificationsEnabled") }
        set { defaults.set(newValue, forKey: "notificationsEnabled") }
    }

    var username: String? {
        get { defaults.string(forKey: "username") }
        set { defaults.set(newValue, forKey: "username") }
    }

    var lastSyncDate: Date? {
        get { defaults.object(forKey: "lastSyncDate") as? Date }
        set { defaults.set(newValue, forKey: "lastSyncDate") }
    }
}
```

**Codableでの使用:**

```swift
extension UserDefaults {
    func setCodable<T: Codable>(_ value: T, forKey key: String) {
        if let encoded = try? JSONEncoder().encode(value) {
            set(encoded, forKey: key)
        }
    }

    func codable<T: Codable>(forKey key: String) -> T? {
        guard let data = data(forKey: key) else { return nil }
        return try? JSONDecoder().decode(T.self, from: data)
    }
}

// 使用例
struct UserPreferences: Codable {
    var theme: String
    var language: String
    var fontSize: Int
}

let preferences = UserPreferences(theme: "dark", language: "ja", fontSize: 14)
UserDefaults.standard.setCodable(preferences, forKey: "userPreferences")

let saved: UserPreferences? = UserDefaults.standard.codable(forKey: "userPreferences")
```

### FileManager

**ファイルの保存と読み込み:**

```swift
class FileStorageManager {
    private let fileManager = FileManager.default

    private var documentsDirectory: URL {
        fileManager.urls(for: .documentDirectory, in: .userDomainMask)[0]
    }

    func save<T: Codable>(_ object: T, filename: String) throws {
        let url = documentsDirectory.appendingPathComponent(filename)
        let data = try JSONEncoder().encode(object)
        try data.write(to: url)
    }

    func load<T: Codable>(filename: String) throws -> T {
        let url = documentsDirectory.appendingPathComponent(filename)
        let data = try Data(contentsOf: url)
        return try JSONDecoder().decode(T.self, from: data)
    }

    func delete(filename: String) throws {
        let url = documentsDirectory.appendingPathComponent(filename)
        try fileManager.removeItem(at: url)
    }

    func fileExists(filename: String) -> Bool {
        let url = documentsDirectory.appendingPathComponent(filename)
        return fileManager.fileExists(atPath: url.path)
    }
}
```

## Core Data

### モデル定義

```swift
// User.xcdatamodeld で定義
// Entity: User
// Attributes: id (UUID), name (String), email (String), createdAt (Date)

extension User {
    static func create(in context: NSManagedObjectContext, name: String, email: String) -> User {
        let user = User(context: context)
        user.id = UUID()
        user.name = name
        user.email = email
        user.createdAt = Date()
        return user
    }
}
```

### Core Data Stack

```swift
class CoreDataManager {
    static let shared = CoreDataManager()

    lazy var persistentContainer: NSPersistentContainer = {
        let container = NSPersistentContainer(name: "AppModel")
        container.loadPersistentStores { description, error in
            if let error = error {
                fatalError("Unable to load persistent stores: \(error)")
            }
        }
        return container
    }()

    var viewContext: NSManagedObjectContext {
        persistentContainer.viewContext
    }

    func save() {
        let context = viewContext
        if context.hasChanges {
            do {
                try context.save()
            } catch {
                let nsError = error as NSError
                fatalError("Unresolved error \(nsError), \(nsError.userInfo)")
            }
        }
    }
}
```

### CRUD操作

```swift
class UserDataStore {
    private let context: NSManagedObjectContext

    init(context: NSManagedObjectContext = CoreDataManager.shared.viewContext) {
        self.context = context
    }

    // Create
    func createUser(name: String, email: String) throws -> User {
        let user = User.create(in: context, name: name, email: email)
        try context.save()
        return user
    }

    // Read
    func fetchUsers() throws -> [User] {
        let request = User.fetchRequest()
        request.sortDescriptors = [NSSortDescriptor(key: "createdAt", ascending: false)]
        return try context.fetch(request)
    }

    func fetchUser(byId id: UUID) throws -> User? {
        let request = User.fetchRequest()
        request.predicate = NSPredicate(format: "id == %@", id as CVarArg)
        return try context.fetch(request).first
    }

    // Update
    func updateUser(_ user: User, name: String?, email: String?) throws {
        if let name = name {
            user.name = name
        }
        if let email = email {
            user.email = email
        }
        try context.save()
    }

    // Delete
    func deleteUser(_ user: User) throws {
        context.delete(user)
        try context.save()
    }
}
```

## キャッシュ戦略

### メモリキャッシュ

```swift
class ImageCache {
    static let shared = ImageCache()

    private let cache = NSCache<NSString, UIImage>()

    private init() {
        cache.countLimit = 100
        cache.totalCostLimit = 50 * 1024 * 1024 // 50MB
    }

    func image(forKey key: String) -> UIImage? {
        cache.object(forKey: key as NSString)
    }

    func setImage(_ image: UIImage, forKey key: String) {
        cache.setObject(image, forKey: key as NSString)
    }

    func removeImage(forKey key: String) {
        cache.removeObject(forKey: key as NSString)
    }

    func clearCache() {
        cache.removeAllObjects()
    }
}
```

### ディスクキャッシュ

```swift
class DiskCache {
    private let fileManager = FileManager.default
    private let cacheDirectory: URL

    init() {
        let urls = fileManager.urls(for: .cachesDirectory, in: .userDomainMask)
        cacheDirectory = urls[0].appendingPathComponent("DiskCache")

        if !fileManager.fileExists(atPath: cacheDirectory.path) {
            try? fileManager.createDirectory(at: cacheDirectory, withIntermediateDirectories: true)
        }
    }

    func save(_ data: Data, forKey key: String) throws {
        let url = cacheDirectory.appendingPathComponent(key)
        try data.write(to: url)
    }

    func data(forKey key: String) -> Data? {
        let url = cacheDirectory.appendingPathComponent(key)
        return try? Data(contentsOf: url)
    }

    func remove(forKey key: String) throws {
        let url = cacheDirectory.appendingPathComponent(key)
        try fileManager.removeItem(at: url)
    }

    func clearCache() throws {
        let contents = try fileManager.contentsOfDirectory(at: cacheDirectory, includingPropertiesForKeys: nil)
        for url in contents {
            try fileManager.removeItem(at: url)
        }
    }
}
```

### 画像ダウンローダー with キャッシュ

```swift
actor ImageDownloader {
    static let shared = ImageDownloader()

    private var inProgressTasks: [URL: Task<UIImage, Error>] = [:]

    func image(from url: URL) async throws -> UIImage {
        // メモリキャッシュをチェック
        if let cached = ImageCache.shared.image(forKey: url.absoluteString) {
            return cached
        }

        // ディスクキャッシュをチェック
        if let data = DiskCache().data(forKey: url.lastPathComponent),
           let image = UIImage(data: data) {
            ImageCache.shared.setImage(image, forKey: url.absoluteString)
            return image
        }

        // 進行中のタスクをチェック
        if let task = inProgressTasks[url] {
            return try await task.value
        }

        // 新しいダウンロードタスクを作成
        let task = Task {
            let (data, _) = try await URLSession.shared.data(from: url)
            guard let image = UIImage(data: data) else {
                throw ImageError.invalidData
            }

            // キャッシュに保存
            ImageCache.shared.setImage(image, forKey: url.absoluteString)
            try? DiskCache().save(data, forKey: url.lastPathComponent)

            return image
        }

        inProgressTasks[url] = task

        defer {
            inProgressTasks[url] = nil
        }

        return try await task.value
    }
}

enum ImageError: Error {
    case invalidData
}
```

## セキュアストレージ

### Keychain

```swift
import Security

class KeychainManager {
    static let shared = KeychainManager()

    private init() {}

    func save(_ data: Data, forKey key: String) throws {
        let query: [String: Any] = [
            kSecClass as String: kSecClassGenericPassword,
            kSecAttrAccount as String: key,
            kSecValueData as String: data
        ]

        SecItemDelete(query as CFDictionary) // 既存のアイテムを削除

        let status = SecItemAdd(query as CFDictionary, nil)
        guard status == errSecSuccess else {
            throw KeychainError.unableToSave
        }
    }

    func load(forKey key: String) throws -> Data {
        let query: [String: Any] = [
            kSecClass as String: kSecClassGenericPassword,
            kSecAttrAccount as String: key,
            kSecReturnData as String: true
        ]

        var result: AnyObject?
        let status = SecItemCopyMatching(query as CFDictionary, &result)

        guard status == errSecSuccess,
              let data = result as? Data else {
            throw KeychainError.itemNotFound
        }

        return data
    }

    func delete(forKey key: String) throws {
        let query: [String: Any] = [
            kSecClass as String: kSecClassGenericPassword,
            kSecAttrAccount as String: key
        ]

        let status = SecItemDelete(query as CFDictionary)
        guard status == errSecSuccess || status == errSecItemNotFound else {
            throw KeychainError.unableToDelete
        }
    }
}

enum KeychainError: Error {
    case unableToSave
    case itemNotFound
    case unableToDelete
}

// トークンの保存・取得例
extension KeychainManager {
    func saveToken(_ token: String) throws {
        guard let data = token.data(using: .utf8) else {
            throw KeychainError.unableToSave
        }
        try save(data, forKey: "authToken")
    }

    func loadToken() throws -> String {
        let data = try load(forKey: "authToken")
        guard let token = String(data: data, encoding: .utf8) else {
            throw KeychainError.itemNotFound
        }
        return token
    }

    func deleteToken() throws {
        try delete(forKey: "authToken")
    }
}
```

## オフライン対応

### ネットワーク監視

```swift
import Network

class NetworkMonitor: ObservableObject {
    static let shared = NetworkMonitor()

    @Published var isConnected = true
    @Published var connectionType: ConnectionType = .unknown

    private let monitor = NWPathMonitor()
    private let queue = DispatchQueue(label: "NetworkMonitor")

    enum ConnectionType {
        case wifi
        case cellular
        case ethernet
        case unknown
    }

    private init() {
        monitor.pathUpdateHandler = { [weak self] path in
            DispatchQueue.main.async {
                self?.isConnected = path.status == .satisfied
                self?.updateConnectionType(path)
            }
        }
        monitor.start(queue: queue)
    }

    private func updateConnectionType(_ path: NWPath) {
        if path.usesInterfaceType(.wifi) {
            connectionType = .wifi
        } else if path.usesInterfaceType(.cellular) {
            connectionType = .cellular
        } else if path.usesInterfaceType(.wiredEthernet) {
            connectionType = .ethernet
        } else {
            connectionType = .unknown
        }
    }
}
```

### オフライン同期

```swift
class SyncManager: ObservableObject {
    @Published var isSyncing = false
    @Published var pendingChanges = 0

    private let repository: UserRepository
    private let localStore: UserDataStore
    private let networkMonitor = NetworkMonitor.shared

    init(repository: UserRepository, localStore: UserDataStore) {
        self.repository = repository
        self.localStore = localStore

        setupNetworkObserver()
    }

    private func setupNetworkObserver() {
        networkMonitor.$isConnected
            .sink { [weak self] isConnected in
                if isConnected {
                    Task { await self?.sync() }
                }
            }
            .store(in: &cancellables)
    }

    func sync() async {
        guard networkMonitor.isConnected else { return }

        isSyncing = true
        defer { isSyncing = false }

        do {
            // サーバーから最新データを取得
            let remoteUsers = try await repository.fetchAllUsers()

            // ローカルデータベースを更新
            for user in remoteUsers {
                try await localStore.upsertUser(user)
            }

            // ローカルの未送信変更を同期
            let pendingUsers = try await localStore.fetchPendingUsers()
            for user in pendingUsers {
                try await repository.updateUser(id: user.id, user)
                try await localStore.markAsSynced(user)
            }

            pendingChanges = 0
        } catch {
            print("Sync failed: \(error)")
        }
    }
}
```

## よくある問題と解決策

### 問題1: ネットワークタイムアウト

**解決策:**
```swift
// リトライロジックの実装
func fetchWithRetry<T>(
    maxRetries: Int = 3,
    delay: TimeInterval = 2.0,
    operation: @escaping () async throws -> T
) async throws -> T {
    var lastError: Error?

    for attempt in 0..<maxRetries {
        do {
            return try await operation()
        } catch {
            lastError = error
            if attempt < maxRetries - 1 {
                try await Task.sleep(nanoseconds: UInt64(delay * 1_000_000_000))
            }
        }
    }

    throw lastError ?? NetworkError.unknown
}

// 使用例
let user = try await fetchWithRetry {
    try await repository.fetchUser(id: 1)
}
```

### 問題2: Core Dataのスレッド安全性

**解決策:**
```swift
// バックグラウンドコンテキストを使用
func performBackgroundTask<T>(_ block: @escaping (NSManagedObjectContext) throws -> T) async throws -> T {
    try await withCheckedThrowingContinuation { continuation in
        persistentContainer.performBackgroundTask { context in
            do {
                let result = try block(context)
                continuation.resume(returning: result)
            } catch {
                continuation.resume(throwing: error)
            }
        }
    }
}
```

### 問題3: メモリリーク（画像キャッシュ）

**解決策:**
```swift
// メモリ警告時にキャッシュをクリア
NotificationCenter.default.addObserver(
    forName: UIApplication.didReceiveMemoryWarningNotification,
    object: nil,
    queue: .main
) { _ in
    ImageCache.shared.clearCache()
}
```

---

**関連Skills:**
- [ios-development](../ios-development/SKILL.md) - iOS開発全般
- [ios-security](../ios-security/SKILL.md) - セキュリティ実装
- [backend-development](../backend-development/SKILL.md) - API設計
- [database-design](../database-design/SKILL.md) - データベース設計

**更新履歴:**
- 2025-12-24: 初版作成

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaku52) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
