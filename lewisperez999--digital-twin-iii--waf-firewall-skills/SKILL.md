---
name: waf-firewall-skills
description: Web Application Firewall and firewall patterns for Next.js 16. Use when implementing rate limiting, bot protection, IP blocking, traffic filtering, and DDoS mitigation. Essential for protecting applications from automated attacks. Use when this capability is needed.
metadata:
  author: lewisperez999
---

# WAF & Firewall Skills

Implement robust Web Application Firewall patterns and traffic filtering for cyber-hardened Next.js 16 applications.

## Table of Contents

1. [Rate Limiting](#rate-limiting)
2. [Bot Detection & Protection](#bot-detection--protection)
3. [IP Blocking & Allowlisting](#ip-blocking--allowlisting)
4. [Request Filtering](#request-filtering)
5. [DDoS Mitigation](#ddos-mitigation)
6. [Geo-Blocking](#geo-blocking)
7. [WAF Rules Engine](#waf-rules-engine)
8. [Integration with Vercel/Cloudflare](#integration-with-vercelcloudflare)

---

## Rate Limiting

### In-Memory Rate Limiter (Development)

```typescript
// lib/security/rate-limit.ts
interface RateLimitEntry {
  count: number;
  resetAt: number;
}

const rateLimitStore = new Map<string, RateLimitEntry>();

export interface RateLimitResult {
  success: boolean;
  remaining: number;
  resetAt: number;
  retryAfter?: number;
}

export async function rateLimit(
  identifier: string,
  endpoint: string,
  limit: number = 100,
  windowMs: number = 60000
): Promise<RateLimitResult> {
  const key = `${identifier}:${endpoint}`;
  const now = Date.now();
  
  let entry = rateLimitStore.get(key);
  
  // Reset if window expired
  if (!entry || now >= entry.resetAt) {
    entry = {
      count: 0,
      resetAt: now + windowMs,
    };
  }
  
  entry.count++;
  rateLimitStore.set(key, entry);
  
  const remaining = Math.max(0, limit - entry.count);
  
  if (entry.count > limit) {
    return {
      success: false,
      remaining: 0,
      resetAt: entry.resetAt,
      retryAfter: Math.ceil((entry.resetAt - now) / 1000),
    };
  }
  
  return {
    success: true,
    remaining,
    resetAt: entry.resetAt,
  };
}

// Cleanup old entries periodically
setInterval(() => {
  const now = Date.now();
  for (const [key, entry] of rateLimitStore.entries()) {
    if (now >= entry.resetAt) {
      rateLimitStore.delete(key);
    }
  }
}, 60000);
```

### Redis Rate Limiter (Production)

```typescript
// lib/security/rate-limit-redis.ts
import { Redis } from '@upstash/redis';

const redis = new Redis({
  url: process.env.UPSTASH_REDIS_REST_URL!,
  token: process.env.UPSTASH_REDIS_REST_TOKEN!,
});

interface RateLimitConfig {
  identifier: string;
  limit: number;
  window: number; // seconds
  endpoint?: string;
}

export async function rateLimitRedis(config: RateLimitConfig): Promise<{
  success: boolean;
  remaining: number;
  reset: number;
}> {
  const { identifier, limit, window, endpoint = 'default' } = config;
  const key = `ratelimit:${endpoint}:${identifier}`;
  
  const pipeline = redis.pipeline();
  pipeline.incr(key);
  pipeline.expire(key, window);
  
  const results = await pipeline.exec();
  const count = results[0] as number;
  
  const remaining = Math.max(0, limit - count);
  const reset = Math.floor(Date.now() / 1000) + window;
  
  return {
    success: count <= limit,
    remaining,
    reset,
  };
}

// Sliding window rate limiter for more accuracy
export async function slidingWindowRateLimit(
  identifier: string,
  limit: number,
  windowSeconds: number
): Promise<boolean> {
  const now = Date.now();
  const windowStart = now - (windowSeconds * 1000);
  const key = `sliding:${identifier}`;
  
  // Remove old entries and add new one
  await redis.zremrangebyscore(key, 0, windowStart);
  await redis.zadd(key, { score: now, member: `${now}:${Math.random()}` });
  await redis.expire(key, windowSeconds);
  
  // Count requests in window
  const count = await redis.zcard(key);
  
  return count <= limit;
}
```

### Rate Limit Middleware

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

// Rate limit configurations per endpoint
const RATE_LIMITS: Record<string, { limit: number; window: number }> = {
  '/api/auth/login': { limit: 5, window: 60 }, // 5 per minute
  '/api/auth/register': { limit: 3, window: 60 }, // 3 per minute
  '/api/contact': { limit: 10, window: 60 }, // 10 per minute
  '/api/': { limit: 100, window: 60 }, // Default API limit
};

function getClientIp(request: NextRequest): string {
  return (
    request.headers.get('cf-connecting-ip') ||
    request.headers.get('x-real-ip') ||
    request.headers.get('x-forwarded-for')?.split(',')[0] ||
    'unknown'
  );
}

export async function middleware(request: NextRequest) {
  const ip = getClientIp(request);
  const pathname = request.nextUrl.pathname;
  
  // Find matching rate limit config
  let config = RATE_LIMITS[pathname];
  if (!config) {
    // Check prefix matches
    for (const [pattern, cfg] of Object.entries(RATE_LIMITS)) {
      if (pathname.startsWith(pattern)) {
        config = cfg;
        break;
      }
    }
  }
  
  if (config) {
    const result = await rateLimit(ip, pathname, config.limit, config.window * 1000);
    
    if (!result.success) {
      // Log rate limit violation
      console.warn('[RATE_LIMIT] Exceeded:', {
        ip,
        pathname,
        timestamp: new Date().toISOString(),
      });
      
      return NextResponse.json(
        { 
          error: 'Too many requests',
          retryAfter: result.retryAfter,
        },
        {
          status: 429,
          headers: {
            'Retry-After': String(result.retryAfter),
            'X-RateLimit-Limit': String(config.limit),
            'X-RateLimit-Remaining': '0',
            'X-RateLimit-Reset': String(result.resetAt),
          },
        }
      );
    }
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: '/api/:path*',
};
```

---

## Bot Detection & Protection

### Bot Detection Middleware

```typescript
// lib/security/bot-detection.ts

interface BotSignals {
  isBot: boolean;
  confidence: number;
  signals: string[];
}

export function detectBot(request: Request): BotSignals {
  const signals: string[] = [];
  let botScore = 0;
  
  const userAgent = request.headers.get('user-agent') || '';
  const acceptLanguage = request.headers.get('accept-language');
  const acceptEncoding = request.headers.get('accept-encoding');
  
  // Known bot user agents
  const botPatterns = [
    /bot/i, /crawler/i, /spider/i, /scraper/i,
    /curl/i, /wget/i, /python/i, /httpie/i,
    /postman/i, /insomnia/i,
  ];
  
  for (const pattern of botPatterns) {
    if (pattern.test(userAgent)) {
      signals.push(`Bot UA pattern: ${pattern}`);
      botScore += 50;
    }
  }
  
  // Missing typical browser headers
  if (!acceptLanguage) {
    signals.push('Missing Accept-Language');
    botScore += 20;
  }
  
  if (!acceptEncoding) {
    signals.push('Missing Accept-Encoding');
    botScore += 15;
  }
  
  // Empty or suspicious user agent
  if (!userAgent || userAgent.length < 10) {
    signals.push('Empty or short User-Agent');
    botScore += 40;
  }
  
  // Check for headless browser indicators
  const headlessIndicators = [
    'headless', 'phantomjs', 'selenium', 'puppeteer', 'playwright',
  ];
  
  for (const indicator of headlessIndicators) {
    if (userAgent.toLowerCase().includes(indicator)) {
      signals.push(`Headless indicator: ${indicator}`);
      botScore += 60;
    }
  }
  
  return {
    isBot: botScore >= 50,
    confidence: Math.min(100, botScore),
    signals,
  };
}

// Honeypot field detection
export function checkHoneypot(formData: FormData, honeypotField: string): boolean {
  const honeypotValue = formData.get(honeypotField);
  return honeypotValue !== null && honeypotValue !== '';
}
```

### CAPTCHA Integration

```typescript
// lib/security/captcha.ts

interface TurnstileResponse {
  success: boolean;
  'error-codes'?: string[];
}

export async function verifyTurnstile(token: string): Promise<boolean> {
  const response = await fetch(
    'https://challenges.cloudflare.com/turnstile/v0/siteverify',
    {
      method: 'POST',
      headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
      body: new URLSearchParams({
        secret: process.env.TURNSTILE_SECRET_KEY!,
        response: token,
      }),
    }
  );
  
  const data: TurnstileResponse = await response.json();
  return data.success;
}

// React component for Turnstile
// components/Turnstile.tsx
'use client';

import { useEffect, useRef } from 'react';

interface TurnstileProps {
  onVerify: (token: string) => void;
  onError?: (error: string) => void;
}

declare global {
  interface Window {
    turnstile: {
      render: (element: HTMLElement, options: object) => string;
      reset: (widgetId: string) => void;
    };
  }
}

export function Turnstile({ onVerify, onError }: TurnstileProps) {
  const containerRef = useRef<HTMLDivElement>(null);
  const widgetIdRef = useRef<string | null>(null);
  
  useEffect(() => {
    if (containerRef.current && window.turnstile) {
      widgetIdRef.current = window.turnstile.render(containerRef.current, {
        sitekey: process.env.NEXT_PUBLIC_TURNSTILE_SITE_KEY!,
        callback: onVerify,
        'error-callback': onError,
      });
    }
  }, [onVerify, onError]);
  
  return <div ref={containerRef} />;
}
```

### Honeypot Form Component

```tsx
// components/SecureForm.tsx
'use client';

import { useState, useRef } from 'react';

interface SecureFormProps {
  children: React.ReactNode;
  action: (formData: FormData) => Promise<void>;
  honeypotField?: string;
}

export function SecureForm({ 
  children, 
  action, 
  honeypotField = '_hp_field' 
}: SecureFormProps) {
  const [submitting, setSubmitting] = useState(false);
  const formRef = useRef<HTMLFormElement>(null);
  const startTimeRef = useRef(Date.now());
  
  async function handleSubmit(formData: FormData) {
    // Check honeypot
    if (formData.get(honeypotField)) {
      console.warn('[BOT_DETECTED] Honeypot triggered');
      return;
    }
    
    // Check submission time (too fast = bot)
    const submissionTime = Date.now() - startTimeRef.current;
    if (submissionTime < 1000) { // Less than 1 second
      console.warn('[BOT_DETECTED] Form submitted too quickly');
      return;
    }
    
    setSubmitting(true);
    try {
      await action(formData);
    } finally {
      setSubmitting(false);
    }
  }
  
  return (
    <form ref={formRef} action={handleSubmit}>
      {children}
      {/* Hidden honeypot field */}
      <input
        type="text"
        name={honeypotField}
        style={{
          position: 'absolute',
          left: '-9999px',
          opacity: 0,
          pointerEvents: 'none',
        }}
        tabIndex={-1}
        autoComplete="off"
      />
      <button type="submit" disabled={submitting}>
        {submitting ? 'Processing...' : 'Submit'}
      </button>
    </form>
  );
}
```

---

## IP Blocking & Allowlisting

### IP Management System

```typescript
// lib/security/ip-management.ts
import { Redis } from '@upstash/redis';

const redis = new Redis({
  url: process.env.UPSTASH_REDIS_REST_URL!,
  token: process.env.UPSTASH_REDIS_REST_TOKEN!,
});

const BLOCKED_IPS_KEY = 'security:blocked_ips';
const ALLOWED_IPS_KEY = 'security:allowed_ips';
const SUSPICIOUS_IPS_KEY = 'security:suspicious_ips';

export async function blockIp(ip: string, reason: string, duration?: number): Promise<void> {
  const entry = JSON.stringify({
    ip,
    reason,
    blockedAt: new Date().toISOString(),
    expiresAt: duration ? new Date(Date.now() + duration).toISOString() : null,
  });
  
  if (duration) {
    await redis.setex(`${BLOCKED_IPS_KEY}:${ip}`, duration / 1000, entry);
  } else {
    await redis.set(`${BLOCKED_IPS_KEY}:${ip}`, entry);
  }
  
  // Log the block
  console.warn('[IP_BLOCKED]', { ip, reason, duration });
}

export async function unblockIp(ip: string): Promise<void> {
  await redis.del(`${BLOCKED_IPS_KEY}:${ip}`);
}

export async function isIpBlocked(ip: string): Promise<boolean> {
  const entry = await redis.get(`${BLOCKED_IPS_KEY}:${ip}`);
  return entry !== null;
}

export async function allowIp(ip: string, note?: string): Promise<void> {
  await redis.sadd(ALLOWED_IPS_KEY, ip);
}

export async function isIpAllowed(ip: string): Promise<boolean> {
  return await redis.sismember(ALLOWED_IPS_KEY, ip);
}

// Track suspicious activity
export async function recordSuspiciousActivity(
  ip: string,
  activity: string
): Promise<number> {
  const key = `${SUSPICIOUS_IPS_KEY}:${ip}`;
  const count = await redis.incr(key);
  await redis.expire(key, 3600); // 1 hour window
  
  // Auto-block after threshold
  if (count >= 10) {
    await blockIp(ip, `Auto-blocked: ${count} suspicious activities`, 24 * 60 * 60 * 1000);
  }
  
  return count;
}
```

### IP Check Middleware

```typescript
// middleware.ts (IP checking section)
import { isIpBlocked, isIpAllowed } from '@/lib/security/ip-management';

export async function middleware(request: NextRequest) {
  const ip = getClientIp(request);
  
  // Check allowlist first (bypass all checks)
  if (await isIpAllowed(ip)) {
    return NextResponse.next();
  }
  
  // Check blocklist
  if (await isIpBlocked(ip)) {
    return NextResponse.json(
      { error: 'Access denied' },
      { status: 403 }
    );
  }
  
  // Continue with other middleware
  return NextResponse.next();
}
```

---

## Request Filtering

### Malicious Request Detection

```typescript
// lib/security/request-filter.ts

interface FilterResult {
  allowed: boolean;
  reason?: string;
  severity?: 'low' | 'medium' | 'high' | 'critical';
}

// SQL injection patterns
const SQL_INJECTION_PATTERNS = [
  /(\b(SELECT|INSERT|UPDATE|DELETE|DROP|UNION|ALTER)\b)/i,
  /('|")\s*(OR|AND)\s*('|"|\d)/i,
  /;\s*(DROP|DELETE|UPDATE|INSERT)/i,
  /\/\*.*\*\//,
  /--\s/,
  /\bEXEC\b/i,
];

// XSS patterns
const XSS_PATTERNS = [
  /<script[^>]*>/i,
  /javascript:/i,
  /on\w+\s*=/i,
  /<iframe/i,
  /<object/i,
  /<embed/i,
  /eval\s*\(/i,
];

// Path traversal patterns
const PATH_TRAVERSAL_PATTERNS = [
  /\.\.\//,
  /\.\.\\/, 
  /%2e%2e%2f/i,
  /%2e%2e\//i,
  /\.\.%2f/i,
];

// Command injection patterns
const COMMAND_INJECTION_PATTERNS = [
  /[;&|`$]/,
  /\$\(/,
  /`[^`]+`/,
];

export function filterRequest(
  url: string,
  body?: string,
  headers?: Record<string, string>
): FilterResult {
  const allContent = [url, body, ...Object.values(headers || {})].join(' ');
  
  // Check SQL injection
  for (const pattern of SQL_INJECTION_PATTERNS) {
    if (pattern.test(allContent)) {
      return {
        allowed: false,
        reason: 'Potential SQL injection detected',
        severity: 'critical',
      };
    }
  }
  
  // Check XSS
  for (const pattern of XSS_PATTERNS) {
    if (pattern.test(allContent)) {
      return {
        allowed: false,
        reason: 'Potential XSS attack detected',
        severity: 'high',
      };
    }
  }
  
  // Check path traversal
  for (const pattern of PATH_TRAVERSAL_PATTERNS) {
    if (pattern.test(url)) {
      return {
        allowed: false,
        reason: 'Path traversal attempt detected',
        severity: 'high',
      };
    }
  }
  
  // Check suspicious content length
  if (body && body.length > 1000000) { // 1MB
    return {
      allowed: false,
      reason: 'Request body too large',
      severity: 'medium',
    };
  }
  
  return { allowed: true };
}
```

### Request Filtering Middleware

```typescript
// middleware.ts (request filtering section)
import { filterRequest } from '@/lib/security/request-filter';
import { recordSuspiciousActivity } from '@/lib/security/ip-management';

export async function middleware(request: NextRequest) {
  const ip = getClientIp(request);
  const url = request.nextUrl.toString();
  
  // Clone request to read body
  let body: string | undefined;
  if (request.method === 'POST' || request.method === 'PUT') {
    try {
      const cloned = request.clone();
      body = await cloned.text();
    } catch {
      // Ignore body read errors
    }
  }
  
  const headers: Record<string, string> = {};
  request.headers.forEach((value, key) => {
    headers[key] = value;
  });
  
  const filterResult = filterRequest(url, body, headers);
  
  if (!filterResult.allowed) {
    // Log attack attempt
    console.error('[WAF_BLOCK]', {
      ip,
      url,
      reason: filterResult.reason,
      severity: filterResult.severity,
      timestamp: new Date().toISOString(),
    });
    
    // Record suspicious activity
    await recordSuspiciousActivity(ip, filterResult.reason || 'Unknown');
    
    return NextResponse.json(
      { error: 'Request blocked by security filter' },
      { status: 403 }
    );
  }
  
  return NextResponse.next();
}
```

---

## DDoS Mitigation

### Adaptive Rate Limiting

```typescript
// lib/security/ddos-protection.ts
import { Redis } from '@upstash/redis';

const redis = new Redis({
  url: process.env.UPSTASH_REDIS_REST_URL!,
  token: process.env.UPSTASH_REDIS_REST_TOKEN!,
});

interface DDoSState {
  totalRequests: number;
  uniqueIps: number;
  startTime: number;
}

export async function checkDDoSThreshold(): Promise<{
  underAttack: boolean;
  requestsPerSecond: number;
}> {
  const windowKey = 'ddos:global:window';
  const ipsKey = 'ddos:global:ips';
  
  const [requests, uniqueIps] = await Promise.all([
    redis.incr(windowKey),
    redis.scard(ipsKey),
  ]);
  
  // Set expiry on first request
  if (requests === 1) {
    await redis.expire(windowKey, 60);
  }
  
  const requestsPerSecond = requests / 60;
  
  // Thresholds for DDoS detection
  const NORMAL_RPS = 100;
  const HIGH_RPS = 500;
  
  return {
    underAttack: requestsPerSecond > HIGH_RPS,
    requestsPerSecond,
  };
}

export async function getDynamicRateLimit(ip: string): Promise<number> {
  const { underAttack, requestsPerSecond } = await checkDDoSThreshold();
  
  if (underAttack) {
    // Severely restrict during attack
    return 10; // 10 requests per minute
  }
  
  if (requestsPerSecond > 200) {
    // Moderate restriction
    return 30;
  }
  
  // Normal operation
  return 100;
}
```

---

## Geo-Blocking

### Country-Based Access Control

```typescript
// lib/security/geo-blocking.ts

// Using Cloudflare or Vercel headers for geo data
export function getGeoData(request: Request): {
  country?: string;
  city?: string;
  region?: string;
} {
  return {
    country: request.headers.get('cf-ipcountry') || 
             request.headers.get('x-vercel-ip-country') || 
             undefined,
    city: request.headers.get('x-vercel-ip-city') || undefined,
    region: request.headers.get('x-vercel-ip-country-region') || undefined,
  };
}

const BLOCKED_COUNTRIES = ['XX', 'YY']; // Example blocked countries
const ALLOWED_COUNTRIES: string[] | null = null; // Set to array to enable allowlist

export function checkGeoAccess(request: Request): {
  allowed: boolean;
  country?: string;
  reason?: string;
} {
  const geo = getGeoData(request);
  
  if (!geo.country) {
    // Allow if geo data unavailable
    return { allowed: true };
  }
  
  // Check blocklist
  if (BLOCKED_COUNTRIES.includes(geo.country)) {
    return {
      allowed: false,
      country: geo.country,
      reason: 'Country blocked',
    };
  }
  
  // Check allowlist (if enabled)
  if (ALLOWED_COUNTRIES && !ALLOWED_COUNTRIES.includes(geo.country)) {
    return {
      allowed: false,
      country: geo.country,
      reason: 'Country not in allowlist',
    };
  }
  
  return { allowed: true, country: geo.country };
}
```

---

## WAF Rules Engine

### Configurable WAF Rules

```typescript
// lib/security/waf-rules.ts

export interface WAFRule {
  id: string;
  name: string;
  enabled: boolean;
  priority: number;
  condition: (request: Request) => boolean | Promise<boolean>;
  action: 'block' | 'log' | 'challenge';
  logLevel: 'info' | 'warn' | 'error';
}

const wafRules: WAFRule[] = [
  {
    id: 'sql-injection',
    name: 'SQL Injection Detection',
    enabled: true,
    priority: 1,
    condition: (request) => {
      const url = request.url;
      return /(\bSELECT\b|\bUNION\b|\bDROP\b)/i.test(url);
    },
    action: 'block',
    logLevel: 'error',
  },
  {
    id: 'scanner-detection',
    name: 'Vulnerability Scanner Detection',
    enabled: true,
    priority: 2,
    condition: (request) => {
      const ua = request.headers.get('user-agent') || '';
      const scannerPatterns = [
        /nikto/i, /sqlmap/i, /nmap/i, /nessus/i,
        /burp/i, /owasp/i, /acunetix/i,
      ];
      return scannerPatterns.some(p => p.test(ua));
    },
    action: 'block',
    logLevel: 'warn',
  },
  {
    id: 'empty-ua',
    name: 'Empty User-Agent',
    enabled: true,
    priority: 10,
    condition: (request) => {
      const ua = request.headers.get('user-agent');
      return !ua || ua.length < 5;
    },
    action: 'challenge',
    logLevel: 'info',
  },
];

export async function evaluateWAFRules(request: Request): Promise<{
  blocked: boolean;
  matchedRule?: WAFRule;
}> {
  // Sort by priority
  const sortedRules = wafRules
    .filter(r => r.enabled)
    .sort((a, b) => a.priority - b.priority);
  
  for (const rule of sortedRules) {
    const matches = await rule.condition(request);
    
    if (matches) {
      // Log the match
      console[rule.logLevel](`[WAF] Rule matched: ${rule.name}`, {
        ruleId: rule.id,
        action: rule.action,
        url: request.url,
      });
      
      if (rule.action === 'block') {
        return { blocked: true, matchedRule: rule };
      }
    }
  }
  
  return { blocked: false };
}
```

---

## Integration with Vercel/Cloudflare

### Vercel Edge Config WAF

```typescript
// lib/security/vercel-waf.ts
import { get } from '@vercel/edge-config';

interface WAFConfig {
  blockedIps: string[];
  blockedCountries: string[];
  rateLimits: Record<string, number>;
  maintenanceMode: boolean;
}

export async function getWAFConfig(): Promise<WAFConfig> {
  const config = await get<WAFConfig>('waf-config');
  return config || {
    blockedIps: [],
    blockedCountries: [],
    rateLimits: { default: 100 },
    maintenanceMode: false,
  };
}
```

### Cloudflare WAF Integration

```typescript
// lib/security/cloudflare-waf.ts

export async function reportToCloudflare(
  ip: string,
  threat: string
): Promise<void> {
  // Report to Cloudflare Security Events API
  await fetch(
    `https://api.cloudflare.com/client/v4/zones/${process.env.CF_ZONE_ID}/security/events`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.CF_API_TOKEN}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        ip,
        threat,
        timestamp: new Date().toISOString(),
      }),
    }
  );
}
```

---

## Best Practices

### WAF Configuration Checklist

1. **Rate Limiting**
   - Implement per-IP and per-endpoint limits
   - Use sliding window for accuracy
   - Include rate limit headers in responses

2. **Bot Protection**
   - Use CAPTCHA for sensitive forms
   - Implement honeypot fields
   - Check submission timing

3. **Request Filtering**
   - Filter SQL injection patterns
   - Block XSS attempts
   - Validate content types

4. **Monitoring**
   - Log all blocked requests
   - Set up alerts for attack patterns
   - Track false positive rate

### Dependencies to Install

```bash
npm install @upstash/redis @vercel/edge-config
```

### Environment Variables

```env
UPSTASH_REDIS_REST_URL=your-redis-url
UPSTASH_REDIS_REST_TOKEN=your-redis-token
TURNSTILE_SECRET_KEY=your-turnstile-secret
NEXT_PUBLIC_TURNSTILE_SITE_KEY=your-turnstile-site-key
CF_ZONE_ID=your-cloudflare-zone-id
CF_API_TOKEN=your-cloudflare-api-token
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lewisperez999) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
