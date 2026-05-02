---
name: hytale-plugin-project-setup
description: Use when creating a new Hytale server plugin project from scratch. Covers Java/Gradle setup, plugin manifest, main class boilerplate, and project structure.
metadata:
  author: darthmolen
---

# Hytale Plugin Project Setup

Use this skill when creating a new Hytale server plugin project from scratch.

## Prerequisites

- **Java 25** - Required by Hytale server
  - Verify: `java -version` should show 25+
- **Gradle** - Build tool (wrapper included in project)
- **Hytale installed** - Via the official launcher (build.gradle auto-detects location)

## Quick Start

Create a new plugin project with this structure:

```text
my-plugin/
├── src/main/
│   ├── java/org/example/plugin/
│   │   ├── MyPlugin.java
│   │   └── MyCommand.java
│   └── resources/
│       └── manifest.json
├── build.gradle
├── settings.gradle
├── gradle.properties
└── gradle/wrapper/
```

## Step 1: settings.gradle

```gradle
rootProject.name = 'MyPlugin'
```

## Step 2: gradle.properties

```properties
# Plugin version (semantic versioning)
version=1.0.0

# Maven group for publishing (usually your package name, not plugin Group)
maven_group=org.example

# Java version - Hytale uses Java 25
java_version=25

# Include assets as a pack (set true if you have models/textures/items)
includes_pack=false

# Hytale release channel (release or pre-release)
patchline=release

# Load mods from user's standard mods folder during development
load_user_mods=false

# Custom Hytale install location (uncomment if needed)
# hytale_home=/path/to/Hytale
```

## Step 3: build.gradle

```gradle
plugins {
    id 'java'
}

import org.gradle.internal.os.OperatingSystem

// Auto-detect Hytale installation
ext {
    if (project.hasProperty('hytale_home')) {
        hytaleHome = project.findProperty('hytale_home')
    } else {
        def os = OperatingSystem.current()
        if (os.isWindows()) {
            hytaleHome = "${System.getProperty("user.home")}/AppData/Roaming/Hytale"
        } else if (os.isMacOsX()) {
            hytaleHome = "${System.getProperty("user.home")}/Library/Application Support/Hytale"
        } else if (os.isLinux()) {
            hytaleHome = "${System.getProperty("user.home")}/.var/app/com.hypixel.HytaleLauncher/data/Hytale"
            if (!file(hytaleHome).exists()) {
                hytaleHome = "${System.getProperty("user.home")}/.local/share/Hytale"
            }
        }
    }
}

if (!project.hasProperty('hytaleHome') || !file(hytaleHome).exists()) {
    throw new GradleException("Hytale install not found. Set hytale_home in gradle.properties.")
}

java {
    toolchain.languageVersion = JavaLanguageVersion.of(java_version)
}

// Reference HytaleServer.jar from game installation
dependencies {
    implementation(files("$hytaleHome/install/$patchline/package/game/latest/Server/HytaleServer.jar"))
}

// Create run directory for local server testing
def serverRunDir = file("$projectDir/run")
if (!serverRunDir.exists()) {
    serverRunDir.mkdirs()
}

// Auto-update manifest.json version from gradle.properties
tasks.register('updatePluginManifest') {
    def manifestFile = file('src/main/resources/manifest.json')
    doLast {
        if (!manifestFile.exists()) {
            throw new GradleException("manifest.json not found!")
        }
        def manifestJson = new groovy.json.JsonSlurper().parseText(manifestFile.text)
        manifestJson.Version = version
        manifestJson.IncludesAssetPack = includes_pack.toBoolean()
        manifestFile.text = groovy.json.JsonOutput.prettyPrint(groovy.json.JsonOutput.toJson(manifestJson))
    }
}

tasks.named('processResources') {
    dependsOn 'updatePluginManifest'
}
```

## Step 4: manifest.json

Create `src/main/resources/manifest.json`:

```json
{
    "Group": "Example",
    "Name": "MyPlugin",
    "Version": "1.0.0",
    "Description": "What this plugin does",
    "Authors": [
        { "Name": "Your Name" }
    ],
    "Website": "example.org",
    "ServerVersion": "*",
    "Dependencies": {},
    "OptionalDependencies": {},
    "DisabledByDefault": false,
    "Main": "org.example.plugin.MyPlugin",
    "IncludesAssetPack": false
}
```

**Critical Notes:**

- `Authors` must be array of objects: `[{"Name": "..."}]` NOT `["..."]`
- `Dependencies` must be object: `{}` NOT `[]`
- `Main` is the fully qualified class name of your plugin class
- `Version` and `IncludesAssetPack` are auto-updated from gradle.properties

## Step 5: Main Plugin Class

Create `src/main/java/org/example/plugin/MyPlugin.java`:

```java
package org.example.plugin;

import com.hypixel.hytale.logger.HytaleLogger;
import com.hypixel.hytale.server.core.plugin.JavaPlugin;
import com.hypixel.hytale.server.core.plugin.JavaPluginInit;

import javax.annotation.Nonnull;

public class MyPlugin extends JavaPlugin {

    private static final HytaleLogger LOGGER = HytaleLogger.forEnclosingClass();

    public MyPlugin(@Nonnull JavaPluginInit init) {
        super(init);
        LOGGER.atInfo().log("Hello from " + this.getName() + " v" + this.getManifest().getVersion());
    }

    @Override
    protected void setup() {
        LOGGER.atInfo().log("Setting up " + this.getName());

        // Register commands
        this.getCommandRegistry().registerCommand(new MyCommand());
    }
}
```

## Step 6: Build and Test

```bash
# Build the JAR
./gradlew build

# JAR will be in build/libs/MyPlugin-1.0.0.jar
# Copy to your Hytale server's mods/ folder
```

## Adding Features

### Register a Command (Simple)

The simplest command uses `CommandBase` with synchronous execution:

```java
package org.example.plugin;

import com.hypixel.hytale.protocol.GameMode;
import com.hypixel.hytale.server.core.Message;
import com.hypixel.hytale.server.core.command.system.CommandContext;
import com.hypixel.hytale.server.core.command.system.basecommands.CommandBase;

import javax.annotation.Nonnull;

public class MyCommand extends CommandBase {

    public MyCommand() {
        super("hello", "Prints a hello message");
        this.setPermissionGroup(GameMode.Adventure); // Anyone can use, not just OP
    }

    @Override
    protected void executeSync(@Nonnull CommandContext ctx) {
        ctx.sendMessage(Message.raw("Hello from the plugin!"));
    }
}
```

### Register a Command (With Arguments)

For commands with arguments, use `AbstractCommand`:

```java
package org.example.plugin.commands;

import com.hypixel.hytale.server.core.Message;
import com.hypixel.hytale.server.core.command.system.AbstractCommand;
import com.hypixel.hytale.server.core.command.system.CommandContext;
import com.hypixel.hytale.server.core.command.system.arguments.system.RequiredArg;
import com.hypixel.hytale.server.core.command.system.arguments.types.ArgTypes;
import com.hypixel.hytale.server.core.command.system.arguments.types.ArgumentType;

import javax.annotation.Nonnull;
import java.util.concurrent.CompletableFuture;

public class GreetCommand extends AbstractCommand {

    @Nonnull
    private final RequiredArg<String> nameArg = this.withRequiredArg(
        "name",
        "The name to greet",
        (ArgumentType) ArgTypes.STRING
    );

    public GreetCommand() {
        super("greet", "Greet someone by name");
    }

    @Override
    protected CompletableFuture<Void> execute(@Nonnull CommandContext context) {
        String name = this.nameArg.get(context);
        context.sendMessage(Message.raw("Hello, " + name + "!"));
        return CompletableFuture.completedFuture(null);
    }
}
```

### Command Collections (Subcommands)

For commands with subcommands like `/mymod greet <name>`:

```java
import com.hypixel.hytale.server.core.command.system.basecommands.AbstractCommandCollection;

public class MyModCommands extends AbstractCommandCollection {

    public MyModCommands() {
        super("mymod", "Commands for MyMod");
        this.addSubCommand(new GreetCommand());
    }
}
// Usage: /mymod greet Alice
```

### Register an Event Listener

```java
import com.hypixel.hytale.server.core.universe.world.events.AddWorldEvent;

@Override
protected void setup() {
    this.getEventRegistry().registerGlobal(AddWorldEvent.class, event -> {
        LOGGER.atInfo().log("World added: %s", event.getWorld().getName());
    });
}
```

### Register a Component (ECS)

Components store data on blocks (ChunkStore) or entities (EntityStore).

Create `components/MyBlockComponent.java`:
```java
package org.example.plugin.components;

import com.hypixel.hytale.codec.Codec;
import com.hypixel.hytale.codec.KeyedCodec;
import com.hypixel.hytale.codec.builder.BuilderCodec;
import com.hypixel.hytale.component.Component;
import com.hypixel.hytale.server.core.universe.world.storage.ChunkStore;

import javax.annotation.Nonnull;

public class MyBlockComponent implements Component<ChunkStore> {

    public static final BuilderCodec<MyBlockComponent> CODEC = BuilderCodec
        .builder(MyBlockComponent.class, MyBlockComponent::new)
        .append(
            new KeyedCodec<>("Value", Codec.INT),
            (comp, val) -> comp.value = val,
            comp -> comp.value
        ).add()
        .build();

    private int value;

    public MyBlockComponent() {}

    public int getValue() { return this.value; }
    public void setValue(int value) { this.value = value; }

    @Nonnull
    @Override
    public Component<ChunkStore> clone() {
        MyBlockComponent copy = new MyBlockComponent();
        copy.value = this.value;
        return copy;
    }
}
```

Register in plugin:
```java
import com.hypixel.hytale.component.ComponentType;

private ComponentType<ChunkStore, MyBlockComponent> myBlockComponent;

@Override
protected void setup() {
    this.myBlockComponent = this.getChunkStoreRegistry().registerComponent(
        MyBlockComponent.class,
        "MyBlockComponent",
        MyBlockComponent.CODEC
    );
}

public ComponentType<ChunkStore, MyBlockComponent> getMyBlockComponent() {
    return this.myBlockComponent;
}
```

**Codec types:** `Codec.INT`, `Codec.FLOAT`, `Codec.STRING`, `Codec.BOOLEAN`, `Codec.UUID_BINARY`, `BuilderCodec.STRING_ARRAY`

### Register a System (ECS)

Systems process components. Use `RefSystem` for add/remove events:

```java
package org.example.plugin.systems;

import com.hypixel.hytale.component.*;
import com.hypixel.hytale.component.query.Query;
import com.hypixel.hytale.component.system.RefSystem;
import com.hypixel.hytale.server.core.universe.world.storage.ChunkStore;
import org.example.plugin.MyPlugin;
import org.example.plugin.components.MyBlockComponent;

import org.checkerframework.checker.nullness.compatqual.NonNullDecl;
import org.checkerframework.checker.nullness.compatqual.NullableDecl;

public class MyBlockSystem extends RefSystem<ChunkStore> {

    @NullableDecl
    @Override
    public Query<ChunkStore> getQuery() {
        return MyPlugin.get().getMyBlockComponent();
    }

    @Override
    public void onEntityAdded(@NonNullDecl Ref<ChunkStore> ref,
                              @NonNullDecl AddReason reason,
                              @NonNullDecl Store<ChunkStore> store,
                              @NonNullDecl CommandBuffer<ChunkStore> commandBuffer) {
        // Block with component placed
    }

    @Override
    public void onEntityRemove(@NonNullDecl Ref<ChunkStore> ref,
                               @NonNullDecl RemoveReason reason,
                               @NonNullDecl Store<ChunkStore> store,
                               @NonNullDecl CommandBuffer<ChunkStore> commandBuffer) {
        if (reason == RemoveReason.UNLOAD) return; // Ignore chunk unloads
        // Block removed
    }
}
```

Register:
```java
import com.hypixel.hytale.component.system.ISystem;

@Override
protected void setup() {
    // Register component first, then system
    this.myBlockComponent = this.getChunkStoreRegistry().registerComponent(...);
    this.getChunkStoreRegistry().registerSystem((ISystem) new MyBlockSystem());
}
```

## With Asset Pack

If your plugin includes models, textures, or items:

1. Set `includes_pack=true` in gradle.properties

2. Assets go in `src/main/resources/`:
   ```text
   src/main/resources/
   ├── manifest.json
   ├── Common/
   │   ├── Blocks/
   │   ├── BlockTextures/
   │   └── Icons/
   └── Server/
       ├── Item/Items/
       └── Languages/
   ```

3. Example item at `src/main/resources/Server/Item/Items/My_Item.json`:
   ```json
   {
       "TranslationProperties": { "Name": "server.items.my_item.name" },
       "MaxStack": 64,
       "Icon": "Icons/my_item.png"
   }
   ```

## Alternative: Separate Asset Pack

For larger projects or when assets need to update independently from code, use a separate `pack/` folder instead of bundling assets in the JAR.

**When to use separate pack folder:**

- Large asset packs (many models/textures)
- Frequent asset updates without code changes
- Collaborative development (artists can update without Java rebuilds)
- Easier debugging (assets visible on disk, not in JAR)

**Project structure with separate pack:**

```text
my-plugin/
├── src/main/
│   ├── java/org/example/plugin/
│   └── resources/
│       └── manifest.json          # Plugin manifest only
├── pack/                          # Separate asset pack
│   ├── manifest.json              # Pack manifest (different from plugin manifest!)
│   ├── Common/
│   │   ├── Blocks/
│   │   ├── BlockTextures/
│   │   └── Icons/
│   └── Server/
│       ├── Item/Items/
│       └── Languages/
├── build.gradle
└── gradle.properties
```

**Pack manifest (pack/manifest.json):**

```json
{
    "Group": "Example",
    "Name": "MyPlugin",
    "Version": "1.0.0",
    "Description": "Asset pack for MyPlugin",
    "Authors": [{ "Name": "Your Name" }],
    "ServerVersion": "*",
    "Dependencies": {},
    "OptionalDependencies": {},
    "DisabledByDefault": false
}
```

Note: Pack manifest does NOT have `Main` or `IncludesAssetPack` fields.

**Plugin manifest (src/main/resources/manifest.json):**

Set `IncludesAssetPack` to `false` since assets are deployed separately:

```json
{
    "IncludesAssetPack": false
}
```

**Deployment gradle tasks:**

Add to build.gradle:

```gradle
// Deploy JAR to server
tasks.register('deployJar', Copy) {
    from tasks.jar
    into '/path/to/hytale-server/mods'
}

// Deploy asset pack to server
tasks.register('deployAssets', Sync) {
    from 'pack'
    into '/path/to/hytale-server/mods/MyPlugin'
}

// Deploy both
tasks.register('deploy') {
    dependsOn 'deployJar', 'deployAssets'
}
```

## CI/CD with GitHub Actions

Add automated builds and releases with GitHub Actions.

**Create `.github/workflows/build.yml`:**

```yaml
name: Build Plugin

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v4

    - name: Setup Java 25
      uses: actions/setup-java@v4
      with:
        java-version: '25'
        distribution: 'temurin'

    - name: Cache Gradle Dependencies
      uses: actions/cache@v4
      with:
        path: ~/.gradle
        key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle*', '**/gradle-wrapper.properties') }}
        restore-keys: |
          ${{ runner.os }}-gradle-

    - name: Grant Execute Permission for Gradlew
      run: chmod +x gradlew

    - name: Build with Gradle
      run: ./gradlew build

    - name: Upload Artifact
      uses: actions/upload-artifact@v4
      with:
        name: plugin-jar
        path: build/libs/*.jar

  release:
    needs: build
    runs-on: ubuntu-latest
    if: startsWith(github.ref, 'refs/tags/v')

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v4

    - name: Setup Java 25
      uses: actions/setup-java@v4
      with:
        java-version: '25'
        distribution: 'temurin'

    - name: Grant Execute Permission for Gradlew
      run: chmod +x gradlew

    - name: Build with Gradle
      run: ./gradlew build

    - name: Create Release
      uses: softprops/action-gh-release@v1
      with:
        files: build/libs/*.jar
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**To create a release:**

```bash
git tag v1.0.0
git push origin v1.0.0
```

This triggers the release job which creates a GitHub release with the JAR attached.

## Troubleshooting

### "Hytale install not found"

- Ensure Hytale is installed via official launcher
- Or set `hytale_home` in gradle.properties

### "Main class not found"

- Verify `Main` in manifest.json matches your class's fully qualified name
- Check package declaration matches folder structure

### "Plugin failed to load"

- Check Java version: `java -version` should show 25+
- Look for errors: `./gradlew build --stacktrace`

## QA Checklist

- [ ] `./gradlew build` completes without errors
- [ ] JAR appears in `build/libs/`
- [ ] manifest.json has correct `Main` class path
- [ ] Server starts without plugin errors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darthmolen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
