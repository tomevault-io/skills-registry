---
name: deployment-and-devops
description: Use when setting up deployment pipelines, Docker environments, CI/CD, or server configuration for Laravel + React applications. Covers Docker Compose, GitHub Actions, Laravel Forge, Laravel Vapor, zero-downtime deployments, and monitoring.
metadata:
  author: bramato
---

# Deployment & DevOps — Laravel + Inertia + React

## 1. Docker Compose Development Environment

A complete local development stack with Laravel, Node (Vite), PostgreSQL, Redis, and Nginx.

### docker-compose.yml

```yaml
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
      target: development
    volumes:
      - .:/var/www/html
      - /var/www/html/vendor
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    environment:
      DB_CONNECTION: pgsql
      DB_HOST: postgres
      DB_PORT: 5432
      DB_DATABASE: app
      DB_USERNAME: app
      DB_PASSWORD: secret
      REDIS_HOST: redis
      CACHE_DRIVER: redis
      SESSION_DRIVER: redis
      QUEUE_CONNECTION: redis
    networks:
      - app-network

  node:
    image: node:20-alpine
    working_dir: /var/www/html
    volumes:
      - .:/var/www/html
      - /var/www/html/node_modules
    command: npm run dev -- --host 0.0.0.0
    ports:
      - "5173:5173"
    networks:
      - app-network

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - .:/var/www/html
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf
      - ./docker/nginx/certs:/etc/nginx/certs
    depends_on:
      - app
    networks:
      - app-network

  postgres:
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
    volumes:
      - postgres-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - app-network

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - app-network

  queue:
    build:
      context: .
      dockerfile: Dockerfile
      target: development
    command: php artisan queue:work --sleep=3 --tries=3 --max-time=3600
    volumes:
      - .:/var/www/html
    depends_on:
      - app
    environment:
      DB_CONNECTION: pgsql
      DB_HOST: postgres
      REDIS_HOST: redis
      QUEUE_CONNECTION: redis
    networks:
      - app-network

volumes:
  postgres-data:
  redis-data:

networks:
  app-network:
    driver: bridge
```

---

## 2. Dockerfile (Multi-Stage Build)

```dockerfile
# ---------- Base ----------
FROM php:8.3-fpm-alpine AS base

RUN apk add --no-cache \
    libpq-dev \
    libzip-dev \
    icu-dev \
    oniguruma-dev \
    && docker-php-ext-install \
    pdo_pgsql \
    pgsql \
    zip \
    intl \
    mbstring \
    opcache \
    pcntl

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

# ---------- Development ----------
FROM base AS development

RUN apk add --no-cache linux-headers $PHPIZE_DEPS \
    && pecl install xdebug redis \
    && docker-php-ext-enable xdebug redis

COPY docker/php/php-dev.ini /usr/local/etc/php/conf.d/99-app.ini

COPY . .
RUN composer install --no-interaction

CMD ["php-fpm"]

# ---------- Production Dependencies ----------
FROM base AS vendor

COPY composer.json composer.lock ./
RUN composer install --no-dev --no-interaction --prefer-dist --optimize-autoloader

# ---------- Frontend Build ----------
FROM node:20-alpine AS frontend

WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

COPY . .
RUN npm run build

# ---------- Production ----------
FROM base AS production

RUN apk add --no-cache \
    && pecl install redis \
    && docker-php-ext-enable redis

COPY docker/php/php-prod.ini /usr/local/etc/php/conf.d/99-app.ini

COPY --chown=www-data:www-data . .
COPY --from=vendor /var/www/html/vendor ./vendor
COPY --from=frontend /app/public/build ./public/build

RUN php artisan config:cache \
    && php artisan route:cache \
    && php artisan view:cache \
    && php artisan event:cache

USER www-data

CMD ["php-fpm"]
```

---

## 3. Development Environment Setup

### Starting the Development Environment

```bash
# First-time setup
docker compose up -d --build
docker compose exec app php artisan key:generate
docker compose exec app php artisan migrate --seed

# Daily development
docker compose up -d

# Vite dev server runs on the node container at http://localhost:5173
# Application is available at http://localhost:80
```

### Vite Configuration for Docker

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import react from '@vitejs/plugin-react';

export default defineConfig({
    plugins: [
        laravel({
            input: 'resources/js/app.tsx',
            refresh: true,
        }),
        react(),
    ],
    server: {
        host: '0.0.0.0',
        port: 5173,
        hmr: {
            host: 'localhost',
        },
    },
});
```

### Nginx Configuration for Development

```nginx
# docker/nginx/default.conf
server {
    listen 80;
    server_name localhost;
    root /var/www/html/public;
    index index.php;

    client_max_body_size 100M;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass app:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_buffer_size 128k;
        fastcgi_buffers 4 256k;
    }

    location ~ /\.(?!well-known) {
        deny all;
    }
}
```

---

## 4. GitHub Actions CI/CD Pipeline

### Test, Build, and Deploy Workflow

```yaml
# .github/workflows/deploy.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, staging]
  pull_request:
    branches: [main]

env:
  PHP_VERSION: '8.3'
  NODE_VERSION: '20'

jobs:
  test-php:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16-alpine
        env:
          POSTGRES_DB: testing
          POSTGRES_USER: testing
          POSTGRES_PASSWORD: secret
        ports: ['5432:5432']
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      redis:
        image: redis:7-alpine
        ports: ['6379:6379']
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ env.PHP_VERSION }}
          extensions: pdo_pgsql, redis, zip, intl, mbstring, pcntl
          coverage: pcov

      - name: Install Composer dependencies
        run: composer install --no-interaction --prefer-dist

      - name: Copy environment file
        run: cp .env.ci .env

      - name: Generate application key
        run: php artisan key:generate

      - name: Run migrations
        run: php artisan migrate --force

      - name: Run Pest tests
        run: php artisan test --parallel --coverage --min=80

      - name: Run Pest architecture tests
        run: ./vendor/bin/pest --filter=Arch

  test-js:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Run TypeScript check
        run: npx tsc --noEmit

      - name: Run ESLint
        run: npx eslint resources/js --ext .ts,.tsx

      - name: Run Vitest
        run: npx vitest run --coverage

  build:
    needs: [test-php, test-js]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/staging'
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: npm

      - name: Install and build frontend
        run: |
          npm ci
          npm run build

      - name: Upload build artifacts
        uses: actions/upload-artifact@v4
        with:
          name: frontend-build
          path: public/build

  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/staging'
    environment: staging
    steps:
      - uses: actions/checkout@v4

      - name: Download build artifacts
        uses: actions/download-artifact@v4
        with:
          name: frontend-build
          path: public/build

      - name: Deploy to staging
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.STAGING_HOST }}
          username: ${{ secrets.STAGING_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            cd /var/www/staging
            git pull origin staging
            composer install --no-dev --optimize-autoloader
            php artisan migrate --force
            php artisan config:cache
            php artisan route:cache
            php artisan view:cache
            php artisan queue:restart

  deploy-production:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - name: Trigger Forge deployment
        run: curl -s "${{ secrets.FORGE_DEPLOY_WEBHOOK }}"
```

---

## 5. Laravel Forge Deployment

### Deploy Script

```bash
cd /home/forge/myapp.com

# Pull latest code
git pull origin $FORGE_SITE_BRANCH

# Install PHP dependencies
composer install --no-dev --no-interaction --prefer-dist --optimize-autoloader

# Install Node dependencies and build frontend
npm ci
npm run build

# Run database migrations
php artisan migrate --force

# Clear and rebuild caches
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan event:cache
php artisan icons:cache    # if using blade-icons

# Restart queue workers gracefully
php artisan queue:restart

# Restart Horizon (if used)
php artisan horizon:terminate

# Reload PHP-FPM for OPcache reset
( flock -w 10 9 || exit 1
    echo 'Restarting FPM...'; sudo -S service php8.3-fpm reload ) 9>/tmp/fpmrestart.lock
```

### Environment Variables on Forge

Set production environment variables through the Forge dashboard under Site > Environment:

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://myapp.com

DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=myapp
DB_USERNAME=forge
DB_PASSWORD=<generated-password>

CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis
REDIS_HOST=127.0.0.1

MAIL_MAILER=ses
AWS_ACCESS_KEY_ID=<key>
AWS_SECRET_ACCESS_KEY=<secret>
AWS_DEFAULT_REGION=us-east-1
```

### Queue Workers on Forge

Configure queue workers under Site > Queue:

- **Connection**: redis
- **Queue**: default,emails,exports
- **Max Tries**: 3
- **Max Seconds**: 3600
- **Processes**: 3
- **Stop When Empty**: No

---

## 6. Laravel Vapor Serverless Deployment

### vapor.yml Configuration

```yaml
id: 12345
name: myapp
environments:
  production:
    memory: 1024
    cli-memory: 512
    runtime: "php-8.3:al2"
    build:
      - "COMPOSER_MIRROR_PATH_REPOS=1 composer install --no-dev"
      - "npm ci && npm run build && rm -rf node_modules"
    deploy:
      - "php artisan migrate --force"
      - "php artisan config:cache"
      - "php artisan route:cache"
      - "php artisan view:cache"
    storage: myapp-production
    database: myapp-production
    cache: myapp-production-cache
    gateway-version: 2
    warm: 10
    concurrency: 50
    timeout: 30
    queues:
      - default
      - emails

  staging:
    memory: 512
    cli-memory: 256
    runtime: "php-8.3:al2"
    build:
      - "COMPOSER_MIRROR_PATH_REPOS=1 composer install --no-dev"
      - "npm ci && npm run build && rm -rf node_modules"
    deploy:
      - "php artisan migrate --force"
    database: myapp-staging
    cache: myapp-staging-cache
    gateway-version: 2
    warm: 2
```

### Vapor Deployment Commands

```bash
# Deploy to staging
vapor deploy staging

# Deploy to production
vapor deploy production

# Rollback to previous deployment
vapor rollback production

# Run artisan commands in production
vapor command production -- migrate:status
vapor command production -- queue:restart

# Tail production logs
vapor tail production
```

---

## 7. Zero-Downtime Deployment Strategies

### Atomic Deployments (Forge / Envoyer)

Atomic deployments create a new release directory for each deploy, then swap a symlink
when the deployment succeeds. If anything fails, the symlink remains on the old release.

```
/home/forge/myapp.com/
  current -> /home/forge/myapp.com/releases/20240115120000
  releases/
    20240115100000/
    20240115120000/    <- current release
  storage/             <- shared across all releases
  .env                 <- shared across all releases
```

### Deployment Checklist for Zero-Downtime

1. **Run migrations before code swap** -- ensure migrations are backward-compatible
2. **Never rename or drop columns in a single deploy** -- use a two-step migration strategy
3. **Warm OPcache** after symlink swap (PHP-FPM reload)
4. **Graceful queue restart** -- `queue:restart` waits for current jobs to finish
5. **Run `npm run build`** in the new release directory before swapping

### Two-Step Migration Strategy

When renaming a column or changing schema in a way that could break running code:

```
Deploy 1: Add new column, write to both old and new
Deploy 2: Drop old column, read from new only
```

---

## 8. Environment Management

### .env per Environment

```
.env                 <- local development (git-ignored)
.env.example         <- committed template
.env.ci              <- CI/CD environment (committed)
.env.testing         <- PHPUnit/Pest environment (committed)
```

### Secret Management

```bash
# Using GitHub Secrets for CI/CD
# Set secrets in GitHub repo settings -> Secrets and variables -> Actions

# Using Forge environment editor
# Forge > Site > Environment > Edit

# Using Vapor secrets
vapor secret production APP_KEY
vapor secret production DB_PASSWORD

# Using AWS SSM Parameter Store (advanced)
aws ssm put-parameter \
  --name "/myapp/production/DB_PASSWORD" \
  --value "secret-password" \
  --type SecureString
```

### Configuration Caching

```bash
# Always cache config in production
php artisan config:cache    # creates bootstrap/cache/config.php
php artisan route:cache     # creates bootstrap/cache/routes-v7.php
php artisan view:cache      # compiles all Blade views
php artisan event:cache     # caches event-listener mappings

# Clear all caches (use during development or debugging)
php artisan optimize:clear
```

---

## 9. Queue Worker Management

### Supervisor Configuration

```ini
# /etc/supervisor/conf.d/myapp-worker.conf
[program:myapp-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/myapp/artisan queue:work redis --sleep=3 --tries=3 --max-time=3600 --max-jobs=1000
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=forge
numprocs=3
redirect_stderr=true
stdout_logfile=/var/www/myapp/storage/logs/worker.log
stopwaitsecs=3600
```

```bash
# Supervisor management
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start myapp-worker:*
sudo supervisorctl status
```

### Laravel Horizon Configuration

```php
// config/horizon.php
'environments' => [
    'production' => [
        'supervisor-1' => [
            'maxProcesses' => 10,
            'balanceMaxShift' => 1,
            'balanceCooldown' => 3,
            'connection' => 'redis',
            'queue' => ['default', 'emails', 'exports'],
            'balance' => 'auto',
            'tries' => 3,
            'timeout' => 300,
            'maxTime' => 3600,
        ],
    ],
    'local' => [
        'supervisor-1' => [
            'maxProcesses' => 3,
            'connection' => 'redis',
            'queue' => ['default', 'emails', 'exports'],
            'balance' => 'simple',
            'tries' => 3,
        ],
    ],
],
```

```bash
# Start Horizon
php artisan horizon

# In production, run Horizon via Supervisor
# /etc/supervisor/conf.d/horizon.conf
# command=php /var/www/myapp/artisan horizon
```

---

## 10. Monitoring and Health Checks

### Health Check Endpoint

```php
// routes/web.php
Route::get('/health', function () {
    $checks = [
        'database' => rescue(fn () => DB::select('SELECT 1') && true, false),
        'cache'    => rescue(fn () => Cache::set('health', true, 10) && Cache::get('health'), false),
        'redis'    => rescue(fn () => Redis::ping() === 'PONG', false),
        'storage'  => rescue(fn () => Storage::put('health.txt', 'ok') && true, false),
    ];

    $healthy = ! in_array(false, $checks, true);

    return response()->json([
        'status' => $healthy ? 'healthy' : 'degraded',
        'checks' => $checks,
        'timestamp' => now()->toISOString(),
    ], $healthy ? 200 : 503);
});
```

### Uptime Monitoring

Configure external uptime monitoring services to poll `/health` and alert on failures.
Common options: UptimeRobot, Oh Dear, Better Uptime, Pingdom.

### Laravel Telescope (Development)

```bash
composer require laravel/telescope --dev
php artisan telescope:install
php artisan migrate
```

Telescope records requests, exceptions, queries, cache operations, queue jobs, mail,
notifications, and scheduled tasks. Only enable in local and staging environments.

### Logging Configuration

```php
// config/logging.php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['daily', 'slack'],
    ],
    'daily' => [
        'driver' => 'daily',
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
        'days' => 14,
    ],
    'slack' => [
        'driver' => 'slack',
        'url' => env('LOG_SLACK_WEBHOOK_URL'),
        'level' => 'error',
    ],
    'sentry' => [
        'driver' => 'sentry',
        'level' => 'warning',
    ],
],
```

---

## 11. Backup Strategies

### Database Backups with spatie/laravel-backup

```bash
composer require spatie/laravel-backup
php artisan vendor:publish --provider="Spatie\Backup\BackupServiceProvider"
```

```php
// config/backup.php
'backup' => [
    'name' => env('APP_NAME', 'myapp'),
    'source' => [
        'files' => [
            'include' => [base_path()],
            'exclude' => [
                base_path('vendor'),
                base_path('node_modules'),
                storage_path(),
            ],
        ],
        'databases' => ['pgsql'],
    ],
    'destination' => [
        'disks' => ['s3'],
    ],
],
'cleanup' => [
    'strategy' => \Spatie\Backup\Tasks\Cleanup\Strategies\DefaultStrategy::class,
    'default_strategy' => [
        'keep_all_backups_for_days' => 7,
        'keep_daily_backups_for_days' => 30,
        'keep_weekly_backups_for_weeks' => 8,
        'keep_monthly_backups_for_months' => 4,
    ],
],
```

```php
// app/Console/Kernel.php or routes/console.php
Schedule::command('backup:run')->dailyAt('02:00');
Schedule::command('backup:clean')->dailyAt('03:00');
Schedule::command('backup:monitor')->dailyAt('04:00');
```

---

## 12. SSL/TLS Configuration

### Forge SSL (Let's Encrypt)

Forge automates Let's Encrypt certificate provisioning and renewal. Enable it through
Site > SSL > Let's Encrypt. Certificates auto-renew every 60 days.

### Nginx SSL Configuration (Manual)

```nginx
server {
    listen 443 ssl http2;
    server_name myapp.com;

    ssl_certificate /etc/nginx/certs/fullchain.pem;
    ssl_certificate_key /etc/nginx/certs/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 1d;
    ssl_session_tickets off;

    # HSTS header
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;

    root /var/www/html/public;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass app:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }
}

server {
    listen 80;
    server_name myapp.com;
    return 301 https://$host$request_uri;
}
```

### Force HTTPS in Laravel

```php
// app/Providers/AppServiceProvider.php
public function boot(): void
{
    if ($this->app->environment('production')) {
        URL::forceScheme('https');
    }
}
```

---

## 13. Performance Monitoring in Production

### OPcache Configuration

```ini
; docker/php/php-prod.ini
opcache.enable=1
opcache.memory_consumption=256
opcache.interned_strings_buffer=64
opcache.max_accelerated_files=20000
opcache.validate_timestamps=0
opcache.save_comments=1
opcache.jit_buffer_size=256M
opcache.jit=1255
```

### PHP-FPM Tuning

```ini
; docker/php/www.conf
pm = dynamic
pm.max_children = 50
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 20
pm.max_requests = 500

; Enable slow log for debugging
slowlog = /var/log/php-fpm-slow.log
request_slowlog_timeout = 5s
```

### Database Query Monitoring

```php
// app/Providers/AppServiceProvider.php
public function boot(): void
{
    // Log slow queries in production
    DB::whenQueryingForLongerThan(500, function (Connection $connection, QueryExecuted $event) {
        Log::warning('Slow query detected', [
            'sql' => $event->sql,
            'time' => $event->time,
            'connection' => $event->connectionName,
        ]);
    });

    // Prevent N+1 queries in non-production environments
    Model::preventLazyLoading(! $this->app->isProduction());
}
```

### Key Performance Metrics to Monitor

| Metric | Target | Tool |
|--------|--------|------|
| Response time (p95) | < 200ms | APM (New Relic, Datadog) |
| Error rate | < 0.1% | Sentry, Bugsnag |
| Queue wait time | < 30s | Horizon dashboard |
| Database query time | < 100ms | Telescope, APM |
| Cache hit ratio | > 95% | Redis INFO stats |
| Memory usage (PHP-FPM) | < 80% limit | Server monitoring |
| Disk usage | < 80% capacity | Server monitoring |
| SSL certificate expiry | > 14 days | Oh Dear, uptime monitor |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bramato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
