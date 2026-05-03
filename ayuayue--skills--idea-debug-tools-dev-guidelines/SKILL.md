---
name: idea-debug-tools-dev-guidelines
description: Comprehensive development guidelines for the debug-tools project. This is an IntelliJ IDEA plugin for Java debugging with hot-deploy, hot-reload, method invocation, and SQL monitoring capabilities. Use when working on the debug-tools project (Maven multi-module + Gradle plugin architecture) to understand: (1) Project structure and module dependencies, (2) Coding standards and conventions, (3) Architecture patterns and design decisions, (4) Build and development workflow, (5) Testing requirements, or any development-related tasks. Use when this capability is needed.
metadata:
  author: ayuayue
---

# Debug Tools Development Guidelines

## Project Overview

Debug Tools is a multi-module Java project with an IntelliJ IDEA plugin that provides advanced debugging capabilities including hot-deploy, hot-reload, method invocation, SQL monitoring, and remote debugging.

### Key Capabilities

- **Hot Deploy**: Skip build/deploy cycle - changes take effect without manual triggers
- **Hot Reload**: Code changes apply instantly without restarting (supports classes, proxies, Spring, Solon, MyBatis)
- **Method Invocation**: Directly invoke any Java method without API layers
- **Remote Debugging**: Trigger remote methods with hot-deploy support
- **SQL Monitoring**: Print SQL statements and execution time without code changes
- **HTTP URL Search**: Navigate from URL to method definition
- **xxl-job Support**: Execute client methods with context passing
- **Groovy Execution**: Run Groovy scripts for debugging

## Module Structure

### Maven Modules (root: `pom.xml`)

```
debug-tools/
├── debug-tools-attach          # Attachment agent
├── debug-tools-common          # Shared utilities, DTOs, protocols
├── debug-tools-test            # Test modules and examples
├── debug-tools-base            # Base functionality
├── debug-tools-server          # Server-side components
├── debug-tools-client          # Client-side components (Netty-based)
├── debug-tools-boot            # Bootstrap and initialization
├── debug-tools-core            # Core debugging functionality
├── debug-tools-hotswap         # Hot swap agent with plugin system
├── debug-tools-vm              # VM-related utilities
├── debug-tools-sql             # SQL monitoring
└── debug-tools-extension       # Extensions (Spring, Solon)
```

### IDEA Plugin Module (Gradle: `debug-tools-idea/`)

```
debug-tools-idea/
├── src/main/java/io/github/future0923/debug/tools/idea/
│   ├── action/                 # IDEA actions (menus, shortcuts)
│   ├── client/socket/          # TCP client for server communication
│   ├── listener/data/          # Data event listeners
│   ├── context/                # Context helpers (class/method data)
│   └── camel/                  # Naming convention converters
└── build.gradle.kts            # Gradle build configuration
```

## Package Structure Standards

### Common Module (`debug-tools-common`)

```
io.github.future0923.debug.tools.common/
├── codec/                      # Netty encoding/decoding
├── dto/                        # Data Transfer Objects
│   ├── RunDTO.java
│   ├── RunContentDTO.java
│   ├── RunResultDTO.java
│   └── TraceMethodDTO.java
├── enums/                      # Enumerations
│   ├── PrintResultType.java
│   ├── ResultClassType.java
│   ├── ResultVarClassType.java
│   └── RunContentType.java
├── exception/                  # Custom exceptions
│   ├── DebugToolsException.java
│   ├── DebugToolsRuntimeException.java
│   ├── ArgsParseException.java
│   └── CallTargetException.java
├── handler/                    # Netty packet handlers
├── protocal/                   # Protocol definitions
│   ├── packet/                 # Packet base and types
│   │   ├── Packet.java
│   │   ├── EntityPacket.java
│   │   ├── request/            # Request packets
│   │   └── response/           # Response packets
│   ├── http/                   # HTTP protocol objects
│   └── serializer/             # Serialization implementations
└── utils/                      # Utility classes
    ├── DebugToolsJsonUtils.java
    ├── DebugToolsTypeUtils.java
    ├── DebugToolsLambdaUtils.java
    ├── DebugToolsDateUtils.java
    └── JdkUnsafeUtils.java
```

### Hot Swap Module (`debug-tools-hotswap`)

```
io.github.future0923.debug.tools.hotswap.core/
├── HotswapAgent.java          # Main agent entry point
├── annotation/                # Plugin annotation system
│   ├── Plugin.java
│   ├── Init.java
│   ├── OnClassFileEvent.java
│   ├── OnClassLoadEvent.java
│   ├── OnResourceFileEvent.java
│   └── handler/               # Annotation processors
├── command/                   # Command system
│   ├── Command.java
│   ├── MergeableCommand.java
│   ├── Scheduler.java
│   └── impl/                  # Implementations
├── config/                    # Configuration
│   ├── PluginManager.java
│   ├── PluginConfiguration.java
│   └── PluginRegistry.java
├── plugin/                    # Built-in plugins
│   ├── hotswapper/            # Hot swap implementation
│   ├── watchResources/        # Resource watching
│   └── gson/jackson/spring/   # Framework-specific plugins
├── util/                      # Utilities
│   ├── JavassistUtil.java     # Bytecode manipulation
│   ├── ReflectionHelper.java
│   ├── HotswapTransformer.java
│   ├── AnnotationHelper.java
│   ├── IOUtils.java
│   ├── classloader/           # ClassLoader utilities
│   └── scanner/               # Class scanning
└── watch/                     # File watching
    ├── Watcher.java
    ├── WatcherFactory.java
    └── nio/                   # NIO2 implementation
```

## Code Style and Conventions

### Import Statements

- Follow standard Java import conventions
- Group imports logically: standard library, third-party, project imports
- Use spotless-maven-plugin for automatic formatting

### Naming Conventions

- **Classes**: PascalCase (e.g., `RunTargetMethodRequestPacket`)
- **Methods**: camelCase (e.g., `dispatchPacket`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `PORT`)
- **Packages**: lowercase with dots (e.g., `io.github.future0923.debug.tools.common`)

### Exception Handling

- Create domain-specific exceptions extending [`DebugToolsException`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/exception/DebugToolsException.java) or [`DebugToolsRuntimeException`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/exception/DebugToolsRuntimeException.java)
- Provide descriptive error messages
- Include relevant context in exception data

### Logging

- Use SLF4J for logging
- Configure logging levels appropriately
- For hotswap operations, see [`LogConfigurationHelper`](debug-tools-hotswap/debug-tools-hotswap-core/src/main/java/io/github/future0923/debug/tools/hotswap/core/config/LogConfigurationHelper.java)

### Lombok Usage

- Lombok is used throughout the project (version 1.18.38)
- Common annotations: `@Data`, `@Slf4j`, `@Builder`, `@AllArgsConstructor`, `@NoArgsConstructor`

## Protocol and Packet Design

### Packet Structure

All packets extend [`Packet`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/protocal/packet/Packet.java):

```java
public abstract class Packet {
    // Common packet fields
    protected Command command;
    protected int sequenceId;
    // ...
}
```

### Request/Response Pattern

- Request packets: `XxxRequestPacket` (in `protocal/packet/request/`)
- Response packets: `XxxResponsePacket` (in `protocal/packet/response/`)
- Use command enum in [`Command`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/protocal/Command.java) for packet type identification

### Serialization

- Default serializer: [`BinaryNettySerializer`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/protocal/serializer/BinaryNettySerializer.java)
- Implement [`NettySerializer`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/protocal/serializer/NettySerializer.java) for custom serialization
- Register serializers in [`SerializerAlgorithm`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/protocal/serializer/SerializerAlgorithm.java)

## Netty Communication

### Client-Side (debug-tools-client / debug-tools-idea)

- Client dispatcher: [`ClientNettyPacketDispatcher`](debug-tools-client/src/main/java/io/github/future0923/debug/tools/client/netty/dispatcher/ClientNettyPacketDispatcher.java) (Netty) or [`ClientPacketDispatcher`](debug-tools-idea/src/main/java/io/github/future0923/debug/tools/idea/client/socket/dispatcher/ClientPacketDispatcher.java) (Socket)
- Handlers are registered in [`ClientDispatchHandler`](debug-tools-client/src/main/java/io/github/future0923/debug/tools/client/netty/handler/ClientDispatchHandler.java)

### Common Codecs

- Encoder: [`PacketNettyEncoder`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/codec/PacketNettyEncoder.java)
- Decoder: [`PacketNettyDecoder`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/codec/PacketNettyDecoder.java)
- Frame decoder: [`PacketFrameDecoder`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/codec/PacketFrameDecoder.java)

### Server-Side (debug-tools-server)

- Handlers implement [`NettyPacketHandler`](debug-tools-common/src/main/java/io/github/future0923/debug/tools/common/handler/NettyPacketHandler.java)

## Hot Swap Plugin System

### Plugin Development

Hot swap uses a plugin architecture. To create a new plugin:

1. Create plugin class with `@Plugin` annotation
2. Use `@Init` for initialization
3. Use event annotations:
   - `@OnClassFileEvent` - Class file changes
   - `@OnClassLoadEvent` - Class loading events
   - `@OnResourceFileEvent` - Resource file changes

Example plugin structure:

```java
@Plugin(name = "MyPlugin", version = "1.0.0")
public class MyPlugin {
    
    @Init
    public void init(PluginConfiguration config) {
        // Initialization logic
    }
    
    @OnClassFileEvent
    public void onClassChange(ClassFileEvent event) {
        // Handle class changes
    }
}
```

### Bytecode Manipulation

- Use [`JavassistUtil`](debug-tools-hotswap/debug-tools-hotswap-core/src/main/java/io/github/future0923/debug/tools/hotswap/core/util/JavassistUtil.java) for bytecode operations
- ClassFileTransformer implementations: [`HaClassFileTransformer`](debug-tools-hotswap/debug-tools-hotswap-core/src/main/java/io/github/future0923/debug/tools/hotswap/core/util/HaClassFileTransformer.java)
- See existing plugins for patterns:
  - [`HotSwapperPlugin`](debug-tools-hotswap/debug-tools-hotswap-core/src/main/java/io/github/future0923/debug/tools/hotswap/core/plugin/hotswapper/HotSwapperPlugin.java)
  - [`WatchResourcesPlugin`](debug-tools-hotswap/debug-tools-hotswap-core/src/main/java/io/github/future0923/debug/tools/hotswap/core/plugin/watchResources/WatchResourcesPlugin.java)

## IDEA Plugin Development

### Action Structure

All IDEA actions extend base classes:

- [`AbstractTraceMethodAction`](debug-tools-idea/src/main/java/io/github/future0923/debug/tools/idea/action/AbstractTraceMethodAction.java) - For method tracing
- Actions are registered in `plugin.xml` (referenced in build.gradle.kts)

### Client Communication

- TCP client: [`DebugToolsTcpClient`](debug-tools-idea/src/main/java/io/github/future0923/debug/tools/idea/client/socket/DebugToolsTcpClient.java)
- Config: [`ClientConfig`](debug-tools-idea/src/main/java/io/github/future0923/debug/tools/idea/client/socket/ClientConfig.java)
- State tracking: [`ClientConnectState`](debug-tools-idea/src/main/java/io/github/future0923/debug/tools/idea/client/socket/ClientConnectState.java)

### Context Helpers

- [`ClassDataContext`](debug-tools-idea/src/main/java/io/github/future0923/debug/tools/idea/context/ClassDataContext.java) - Class information
- [`MethodDataContext`](debug-tools-idea/src/main/java/io/github/future0923/debug/tools/idea/context/MethodDataContext.java) - Method information

## Build and Development

### Maven Build

```bash
# Build entire project
mvn clean install

# Build specific module
cd debug-tools-common
mvn clean install

# Skip tests
mvn clean install -DskipTests
```

### Gradle Build (IDEA Plugin)

```bash
cd debug-tools-idea
./gradlew buildPlugin

# For Windows
gradlew.bat buildPlugin
```

### Code Formatting

- spotless-maven-plugin runs automatically on `validate` phase
- Manually format: `mvn spotless:apply`

### License Formatting

- license-maven-plugin runs automatically on `validate` phase
- See HEADER file for license template

## Testing

### Test Modules

- `debug-tools-test/` contains test applications:
  - `debug-tools-test-simple` - Simple test case
  - `debug-tools-test-spring-boot-mybatis` - Spring Boot + MyBatis example

### Running Tests

```bash
# Run all tests
mvn test

# Run tests with hotswap agent (JDK < 21)
mvn test -P surefire-java-lt21
```

### Test Configuration

Hotswap tests require special JVM arguments (see `surefire-java-lt21` profile in pom.xml):

```bash
-XX:+AllowEnhancedClassRedefinition
-XX:HotswapAgent=external
--add-opens java.base/java.lang=ALL-UNNAMED
--add-opens java.base/jdk.internal.loader=ALL-UNNAMED
# ... more opens
```

## Dependencies

### Key Versions

- Java: 8+ (supports 8, 11, 17, 21, 25)
- Spring Boot: 2.7.4
- Groovy: 4.0.22
- Javassist: 3.30.2-GA
- Lombok: 1.18.38
- Hutool: 5.8.29
- Arthas: 3.7.2

### Shadow/Relocated Packages

- Shaded package prefix: `io.github.future0923.debug.tools.dependencies`
- Use maven-shade-plugin to relocate third-party dependencies

## Framework Integration

### Spring Plugin

Location: `debug-tools-hotswap/debug-tools-hotswap-plugin/debug-tools-hotswap-spring-plugin`

- Supports Spring Boot 2.7.4
- Provides Spring context-aware hot reload

### Solon Plugin

Location: `debug-tools-extension/debug-tools-extension-solon`

- Supports Solon 3.3.1
- Provides Solon framework integration

### Jackson/Gson Plugins

- [`JacksonPlugin`](debug-tools-hotswap/debug-tools-hotswap-plugin/debug-tools-hotswap-jackson-plugin/src/main/java/io/github/future0923/debug/tools/hotswap/core/plugin/jackson/JacksonPlugin.java)
- [`GsonPlugin`](debug-tools-hotswap/debug-tools-hotswap-plugin/debug-tools-hotswap-gson-plugin/src/main/java/io/github/future0923/debug/tools/hotswap/core/plugin/gson/GsonPlugin.java)

## Development Workflow

1. **Code Changes**: Make modifications in appropriate modules
2. **Build**: Run `mvn clean install` for Maven modules
3. **Plugin Build**: Run `./gradlew buildPlugin` in debug-tools-idea
4. **Test**: Use test applications in debug-tools-test
5. **IDEA Plugin**: Install plugin jar from `debug-tools-idea/build/distributions/`

## Important Files to Reference

- Main POM: [`pom.xml`](pom.xml)
- Hotswap Agent: [`HotswapAgent.java`](debug-tools-hotswap/debug-tools-hotswap-core/src/main/java/io/github/future0923/debug/tools/hotswap/core/HotswapAgent.java)
- Client Config: [`ClientConfig.java`](debug-tools-client/src/main/java/io/github/future0923/debug/tools/client/config/ClientConfig.java)
- Bootstrap: [`DebugToolsBootstrap.java`](debug-tools-boot/src/main/java/io/github/future0923/debug/tools/boot/DebugToolsBootstrap.java)
- IDEA Plugin Build: [`build.gradle.kts`](debug-tools-idea/build.gradle.kts)

## Important Notes

- **DO NOT execute mvn or other build commands during development**
- **DO NOT write test code**
- **Prompt users to manually build and verify changes**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayuayue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
