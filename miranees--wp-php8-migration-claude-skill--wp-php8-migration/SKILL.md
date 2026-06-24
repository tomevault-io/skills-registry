---
name: wp-php8-migration
description: PHP 8.x migration guide for WordPress — covers PHP 8.0 through 8.3 features, breaking changes, backward compatibility patterns, dynamic properties fixes, and step-by-step migration strategy for themes, plugins, and custom code. Use when this capability is needed.
metadata:
  author: miranees
---

# PHP 8.x Migration for WordPress

Complete reference for migrating WordPress themes, plugins, and custom code from PHP 7.4 to PHP 8.0, 8.1, 8.2, and 8.3. Covers new features, breaking changes, backward compatibility, and the most common migration patterns.

---

## 1. PHP 8.0 Features for WordPress

### Named Arguments

Use with caution in WordPress hook callbacks. WordPress core functions do not guarantee parameter name stability across versions.

```php
// BEFORE (PHP 7.4)
wp_insert_post( array(
    'post_title'   => 'My Post',
    'post_content' => 'Content here',
    'post_status'  => 'publish',
    'post_type'    => 'page',
) );

// AFTER (PHP 8.0) — named arguments in your OWN functions only
function register_custom_block( string $name, string $title, string $icon = 'smiley', string $category = 'widgets' ): void {
    // ...
}
register_custom_block( name: 'my-block', title: 'My Block', category: 'layout' );

// WARNING: Do NOT use named arguments with WordPress core functions or hooks.
// Parameter names may change between WP versions and break your code.
```

### Union Types for Hook Returns

```php
// BEFORE (PHP 7.4)
/** @param string|array $classes */
function filter_body_class( $classes ) { ... }

// AFTER (PHP 8.0)
function filter_body_class( string|array $classes ): string|array {
    if ( is_array( $classes ) ) {
        $classes[] = 'custom-class';
    }
    return $classes;
}
```

### Nullsafe Operator for Chained WP Calls

```php
// BEFORE (PHP 7.4)
$user = wp_get_current_user();
$name = null;
if ( $user !== null ) {
    $meta = get_user_meta( $user->ID, 'display_name', true );
    if ( $meta !== null ) {
        $name = $meta;
    }
}

// AFTER (PHP 8.0)
$name = wp_get_current_user()?->display_name;
```

### Match Expressions Replacing Switch Statements

```php
// BEFORE (PHP 7.4)
switch ( $post->post_status ) {
    case 'publish':
        $label = 'Published';
        break;
    case 'draft':
        $label = 'Draft';
        break;
    case 'pending':
        $label = 'Pending Review';
        break;
    default:
        $label = 'Unknown';
        break;
}

// AFTER (PHP 8.0)
$label = match ( $post->post_status ) {
    'publish' => 'Published',
    'draft'   => 'Draft',
    'pending' => 'Pending Review',
    default   => 'Unknown',
};
```

### Constructor Promotion for Plugin Classes

```php
// BEFORE (PHP 7.4)
class My_Plugin {
    private string $plugin_file;
    private string $version;
    private bool $debug;

    public function __construct( string $plugin_file, string $version, bool $debug = false ) {
        $this->plugin_file = $plugin_file;
        $this->version     = $version;
        $this->debug       = $debug;
    }
}

// AFTER (PHP 8.0)
class My_Plugin {
    public function __construct(
        private string $plugin_file,
        private string $version,
        private bool $debug = false,
    ) {}
}
```

### str_contains / str_starts_with / str_ends_with

```php
// BEFORE (PHP 7.4)
if ( strpos( $template, 'single-' ) === 0 ) { ... }
if ( strpos( $content, 'shortcode' ) !== false ) { ... }
if ( substr( $file, -4 ) === '.php' ) { ... }

// AFTER (PHP 8.0)
if ( str_starts_with( $template, 'single-' ) ) { ... }
if ( str_contains( $content, 'shortcode' ) ) { ... }
if ( str_ends_with( $file, '.php' ) ) { ... }
```

**Note:** WordPress 5.9+ includes polyfills for these functions. For older WP versions, use `wp_polyfill` or provide your own.

---

## 2. PHP 8.1 Features for WordPress

### Enums for WordPress Constants

```php
// BEFORE (PHP 7.4) — string constants scattered everywhere
define( 'POST_STATUS_PUBLISH', 'publish' );
define( 'POST_STATUS_DRAFT', 'draft' );

// AFTER (PHP 8.1)
enum PostStatus: string {
    case Publish = 'publish';
    case Draft   = 'draft';
    case Pending = 'pending';
    case Private = 'private';
    case Trash   = 'trash';

    public function label(): string {
        return match ( $this ) {
            self::Publish => __( 'Published', 'my-plugin' ),
            self::Draft   => __( 'Draft', 'my-plugin' ),
            self::Pending => __( 'Pending Review', 'my-plugin' ),
            self::Private => __( 'Private', 'my-plugin' ),
            self::Trash   => __( 'Trashed', 'my-plugin' ),
        };
    }
}

// Usage with WP_Query
$query = new WP_Query( [ 'post_status' => PostStatus::Publish->value ] );
```

### Readonly Properties for Immutable Config

```php
// BEFORE (PHP 7.4)
class Plugin_Config {
    private string $slug;
    public function __construct( string $slug ) {
        $this->slug = $slug;
    }
    public function get_slug(): string { return $this->slug; }
}

// AFTER (PHP 8.1)
class Plugin_Config {
    public function __construct(
        public readonly string $slug,
        public readonly string $version,
        public readonly string $file,
    ) {}
}
// $config->slug is publicly readable but cannot be modified after construction.
```

### First-Class Callables for Hook Registration

```php
// BEFORE (PHP 7.4)
add_action( 'init', [ $this, 'register_post_types' ] );
add_filter( 'the_content', [ $this, 'filter_content' ] );

// AFTER (PHP 8.1)
add_action( 'init', $this->register_post_types( ... ) );
add_filter( 'the_content', $this->filter_content( ... ) );

// WARNING: Closure::fromCallable() or the ( ... ) syntax creates a Closure.
// remove_action / remove_filter will NOT work because object identity differs.
// Use first-class callables only when you never need to unhook.
```

### Intersection Types for WP Interfaces

```php
// PHP 8.1 — require multiple interfaces
function process_entity( Countable&Iterator $items ): void {
    foreach ( $items as $item ) {
        // guaranteed to be both Countable and Iterator
    }
}
```

---

## 3. PHP 8.2 Features for WordPress

### Readonly Classes for Value Objects

```php
// PHP 8.2
readonly class Meta_Box_Args {
    public function __construct(
        public string $id,
        public string $title,
        public string $screen,
        public string $context = 'advanced',
        public string $priority = 'default',
    ) {}
}
```

### Deprecated Dynamic Properties (Massive WP Impact)

This is the single largest PHP 8.2 issue for WordPress. See Section 9 for full remediation.

```php
// PHP 8.2 DEPRECATION — dynamic properties trigger E_DEPRECATED
$obj = new stdClass(); // stdClass is exempt
$obj->foo = 'bar';     // fine on stdClass

class My_Widget extends WP_Widget {
    // This triggers deprecation in PHP 8.2:
    // $this->custom_prop = 'value';
}
```

### null, false, true as Standalone Types

```php
// PHP 8.2
function wp_cache_get_or_set( string $key ): string|false {
    $cached = wp_cache_get( $key );
    if ( $cached === false ) {
        return false; // explicitly typed as false
    }
    return $cached;
}
```

---

## 4. PHP 8.3 Features for WordPress

### Typed Class Constants

```php
// BEFORE (PHP 8.2)
class My_REST_Controller extends WP_REST_Controller {
    const NAMESPACE = 'myplugin/v1'; // untyped
}

// AFTER (PHP 8.3)
class My_REST_Controller extends WP_REST_Controller {
    const string NAMESPACE = 'myplugin/v1';
    const int    VERSION   = 1;
}
```

### json_validate()

```php
// BEFORE (PHP 8.2)
function is_valid_json( string $data ): bool {
    json_decode( $data );
    return json_last_error() === JSON_ERROR_NONE;
}

// AFTER (PHP 8.3) — no decoding overhead
if ( json_validate( $raw_meta ) ) {
    $meta = json_decode( $raw_meta, true );
}
```

### #[\Override] Attribute for Template Methods

```php
// PHP 8.3
class Theme_Walker extends Walker_Nav_Menu {
    #[\Override]
    public function start_el( &$output, $item, $depth = 0, $args = null, $id = 0 ): void {
        // If parent signature changes, PHP will throw a compile-time error.
    }
}
```

---

## 5. Breaking Changes and Gotchas

### Stricter Type Coercion (8.0)

```php
// PHP 7.4: silently coerces — strlen( [] ) returns null with a warning
// PHP 8.0: throws TypeError for internal function type mismatches

// FIX: Always validate types before passing to internal functions
$length = is_string( $value ) ? strlen( $value ) : 0;
```

### Internal Function Signature Enforcement (8.0)

```php
// PHP 7.4: passing null to non-nullable internal parameter gives a warning
// PHP 8.0+: still a warning; PHP 8.1: deprecation notice; PHP 9.0: TypeError

// Common WP offender:
trim( null );           // Deprecated in 8.1
htmlspecialchars( null ); // Deprecated in 8.1

// FIX:
trim( $value ?? '' );
htmlspecialchars( $value ?? '' );
```

### String Interpolation Changes (8.2)

```php
// PHP 8.2 DEPRECATED: ${var} inside strings
$msg = "Hello ${name}";  // deprecated
$msg = "Hello {$name}";  // correct — use this form
$msg = "Hello $name";    // also fine for simple variables
```

### Implicit Float-to-Int Conversions (8.1)

```php
// PHP 8.1 DEPRECATED: implicit narrowing float-to-int
$index = 3.0;
$arr[$index]; // deprecated — use (int) $index explicitly
```

---

## 6. WordPress Core PHP Compatibility

### Minimum PHP Version Roadmap

| WordPress Version | Minimum PHP | Recommended PHP |
|---|---|---|
| WP 5.9 - 6.2 | 5.6 | 7.4+ |
| WP 6.3 - 6.4 | 7.0 | 8.0+ |
| WP 6.5+ | 7.2+ | 8.1+ |

Always check the latest `readme.html` in WP core for the current minimum.

### Setting Minimum PHP in Plugin Headers

```php
/**
 * Plugin Name: My Plugin
 * Requires PHP: 8.0
 * Requires at least: 6.3
 */
```

WordPress will prevent activation on incompatible PHP versions when `Requires PHP` is set.

### Site Health PHP Check

WordPress Site Health (Tools > Site Health) checks PHP version and reports recommendations. Programmatic check:

```php
$compat = wp_check_php_version();
if ( $compat && isset( $compat['is_acceptable'] ) && ! $compat['is_acceptable'] ) {
    add_action( 'admin_notices', 'show_php_upgrade_notice' );
}
```

---

## 7. Migration Strategy

### Step-by-Step Migration Path

**Phase 1: Audit**

```bash
# Install PHPCompatibility coding standard
composer require --dev phpcompatibility/phpcompatibility-wp:"*"

# Configure phpcs
cat > phpcs.xml <<'XML'
<?xml version="1.0"?>
<ruleset name="PHP8Migration">
    <rule ref="PHPCompatibilityWP"/>
    <config name="testVersion" value="8.0-"/>
    <file>./wp-content/themes/oshin_child/</file>
    <file>./wp-content/plugins/my-plugin/</file>
</ruleset>
XML

# Run the scan
vendor/bin/phpcs --standard=phpcs.xml --report=full
```

**Phase 2: Fix Deprecations**

Address issues in order of severity:
1. Fatal errors (TypeError from internal functions)
2. Deprecation notices (dynamic properties, string interpolation)
3. Behavioral changes (stricter comparisons, match vs switch)

**Phase 3: CI Matrix Testing**

```yaml
# GitHub Actions example
strategy:
  matrix:
    php: ['7.4', '8.0', '8.1', '8.2', '8.3']
    wordpress: ['6.3', '6.5', 'latest']
steps:
  - uses: shivammathur/setup-php@v2
    with:
      php-version: ${{ matrix.php }}
  - run: vendor/bin/phpunit
  - run: vendor/bin/phpcs --standard=phpcs.xml
```

**Phase 4: Rollout**

1. Deploy to staging with PHP 8.x
2. Run full test suite and manual smoke test
3. Monitor `debug.log` for deprecation notices
4. Deploy to production with monitoring

---

## 8. Backward Compatibility Patterns

### Feature Detection

```php
// Function-level polyfill guard
if ( ! function_exists( 'str_contains' ) ) {
    function str_contains( string $haystack, string $needle ): bool {
        return '' === $needle || false !== strpos( $haystack, $needle );
    }
}

// Class-level detection
if ( class_exists( 'WeakMap' ) ) {
    $cache = new WeakMap();
} else {
    $cache = new SplObjectStorage();
}
```

### Version Gating

```php
// Use PHP 8.1 enums only when available
if ( PHP_VERSION_ID >= 80100 ) {
    require_once __DIR__ . '/includes/enums.php';
} else {
    require_once __DIR__ . '/includes/enum-compat.php';
}
```

### Dual-Syntax Attribute Pattern

```php
// PHP 8.0 attributes are not parsed on PHP 7.x (syntax error).
// Use doc-block annotations as fallback with a framework that reads both.
// Or simply require PHP 8.0+ and drop 7.x support.

#[Route('/api/posts')]       // PHP 8.0+
/** @Route("/api/posts") */  // PHP 7.x fallback (needs annotation reader)
```

---

## 9. Dynamic Properties Fix (PHP 8.2)

This is the number one migration issue. Thousands of WordPress plugins and themes use dynamic properties.

### Solution 1: Declare Properties Explicitly (Preferred)

```php
// BEFORE — dynamic property usage
class My_Widget extends WP_Widget {
    public function __construct() {
        parent::__construct( 'my_widget', 'My Widget' );
        $this->custom_option = get_option( 'my_widget_opt' ); // DEPRECATED in 8.2
    }
}

// AFTER — declare the property
class My_Widget extends WP_Widget {
    private string $custom_option;

    public function __construct() {
        parent::__construct( 'my_widget', 'My Widget' );
        $this->custom_option = get_option( 'my_widget_opt' ) ?: '';
    }
}
```

### Solution 2: #[\AllowDynamicProperties] Attribute

```php
// Quick fix for large classes where full audit is impractical
#[\AllowDynamicProperties]
class Legacy_Plugin_Core {
    // Dynamic properties still work without deprecation notices.
    // Plan to refactor and remove this attribute.
}
```

### Solution 3: Magic Methods (__get / __set)

```php
class Extensible_Base {
    private array $data = [];

    public function __get( string $name ): mixed {
        return $this->data[ $name ] ?? null;
    }

    public function __set( string $name, mixed $value ): void {
        $this->data[ $name ] = $value;
    }

    public function __isset( string $name ): bool {
        return isset( $this->data[ $name ] );
    }
}
```

### Audit Pattern for Finding Dynamic Property Usage

```bash
# Find assignments to $this-> that are not declared properties
# Run from plugin/theme root
grep -rn '\$this->[a-z_]*\s*=' --include='*.php' . | \
  while read -r line; do
    prop=$(echo "$line" | grep -oP '\$this->\K[a-z_]+')
    file=$(echo "$line" | cut -d: -f1)
    if ! grep -qP "^\s*(public|protected|private|var)\s+.*\\\$$prop" "$file"; then
      echo "DYNAMIC: $line"
    fi
  done
```

---

## 10. Common Migration Patterns (Top 20)

### Pattern 1: strpos to str_contains

```php
// BEFORE
if ( strpos( $haystack, $needle ) !== false ) { ... }
// AFTER
if ( str_contains( $haystack, $needle ) ) { ... }
```

### Pattern 2: substr check to str_starts_with

```php
// BEFORE
if ( substr( $str, 0, 3 ) === 'wp-' ) { ... }
// AFTER
if ( str_starts_with( $str, 'wp-' ) ) { ... }
```

### Pattern 3: substr end check to str_ends_with

```php
// BEFORE
if ( substr( $file, -4 ) === '.php' ) { ... }
// AFTER
if ( str_ends_with( $file, '.php' ) ) { ... }
```

### Pattern 4: Null coalescing in place of isset ternary

```php
// BEFORE
$val = isset( $_GET['page'] ) ? $_GET['page'] : 'default';
// AFTER (PHP 7.0+, but still under-used)
$val = $_GET['page'] ?? 'default';
```

### Pattern 5: Null-safe operator chains

```php
// BEFORE
$avatar = '';
if ( $user && $user->get_avatar_url() ) { $avatar = $user->get_avatar_url(); }
// AFTER
$avatar = $user?->get_avatar_url() ?? '';
```

### Pattern 6: match() replacing switch with return

```php
// BEFORE
switch ( $type ) { case 'post': return 'Posts'; case 'page': return 'Pages'; default: return 'Items'; }
// AFTER
return match ( $type ) { 'post' => 'Posts', 'page' => 'Pages', default => 'Items' };
```

### Pattern 7: Constructor promotion

```php
// BEFORE
class Service { private Logger $logger; public function __construct( Logger $logger ) { $this->logger = $logger; } }
// AFTER
class Service { public function __construct( private Logger $logger ) {} }
```

### Pattern 8: Union types for WP filter returns

```php
// BEFORE
/** @return string|false */
function my_filter( $val ) { ... }
// AFTER
function my_filter( string $val ): string|false { ... }
```

### Pattern 9: Null parameter to internal functions

```php
// BEFORE (triggers deprecation in 8.1)
$clean = trim( $value );       // $value might be null
// AFTER
$clean = trim( $value ?? '' );
```

### Pattern 10: array_key_exists vs isset

```php
// PHP 8.0 removed array_key_exists() on objects. Use property_exists() instead.
// BEFORE
array_key_exists( 'key', $object ); // Fatal in 8.0
// AFTER
property_exists( $object, 'key' );
// For arrays, array_key_exists() still works fine.
```

### Pattern 11: Sorting comparison functions

```php
// PHP 8.0: comparison functions must return int, not bool
// BEFORE
usort( $arr, function( $a, $b ) { return $a > $b; } ); // returns bool — broken
// AFTER
usort( $arr, function( $a, $b ) { return $a <=> $b; } ); // spaceship operator
```

### Pattern 12: Named arguments in array functions

```php
// PHP 8.0 named args work well with long parameter lists
// BEFORE
array_slice( $posts, 0, 10, true );
// AFTER
array_slice( $posts, offset: 0, length: 10, preserve_keys: true );
```

### Pattern 13: Enum replacing class constants

```php
// BEFORE
class Capability { const EDIT = 'edit_posts'; const DELETE = 'delete_posts'; }
// AFTER (PHP 8.1)
enum Capability: string { case Edit = 'edit_posts'; case Delete = 'delete_posts'; }
```

### Pattern 14: Readonly properties for settings

```php
// BEFORE
class Settings { private string $option_name; public function get_name(): string { return $this->option_name; } }
// AFTER (PHP 8.1)
class Settings { public function __construct( public readonly string $option_name ) {} }
```

### Pattern 15: Fibers for deferred operations (Advanced)

```php
// PHP 8.1 Fibers — potential for async WP operations
$fiber = new Fiber( function (): void {
    $data = Fiber::suspend( 'waiting_for_api' );
    update_option( 'api_result', $data );
} );
$fiber->start();
// ... do other work ...
$fiber->resume( wp_remote_get( 'https://api.example.com/data' ) );
```

### Pattern 16: First-class callable syntax for hooks

```php
// BEFORE
add_action( 'wp_enqueue_scripts', [ $this, 'enqueue' ] );
// AFTER (PHP 8.1) — creates a Closure
add_action( 'wp_enqueue_scripts', $this->enqueue( ... ) );
```

### Pattern 17: Intersection types for strict interfaces

```php
// PHP 8.1
function render( Renderable&Cacheable $component ): string { ... }
```

### Pattern 18: DNF types (PHP 8.2)

```php
// Disjunctive Normal Form types
function get_post_data( int $id ): (Countable&Traversable)|false {
    $result = $wpdb->get_results( "..." );
    return $result ?: false;
}
```

### Pattern 19: json_validate before decode (PHP 8.3)

```php
// BEFORE
$decoded = json_decode( $input, true );
if ( json_last_error() !== JSON_ERROR_NONE ) { return new WP_Error(); }
// AFTER
if ( ! json_validate( $input ) ) { return new WP_Error( 'invalid_json' ); }
$decoded = json_decode( $input, true );
```

### Pattern 20: #[\Override] for WordPress template methods

```php
// PHP 8.3 — catches signature drift in parent classes after WP updates
class Custom_List_Table extends WP_List_Table {
    #[\Override]
    public function get_columns(): array { ... }

    #[\Override]
    public function prepare_items(): void { ... }
}
```

---

## Quick Reference: Minimum PHP per Feature

| Feature | Minimum PHP |
|---|---|
| Named arguments, union types, match, nullsafe, str_contains | 8.0 |
| Enums, readonly props, fibers, first-class callables, intersection types | 8.1 |
| Readonly classes, DNF types, deprecated dynamic properties | 8.2 |
| Typed constants, json_validate, #[\Override] | 8.3 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/miranees) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
