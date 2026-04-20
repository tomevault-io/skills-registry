---
name: threat-telemetry-skills
description: Security logging, monitoring, attack detection, and threat telemetry dashboard for Next.js 16. Use when implementing security event logging, attack monitoring, CVSS scoring, real-time threat visualization, and security analytics dashboards. Use when this capability is needed.
metadata:
  author: lewisperez999
---

# Threat Telemetry Skills

Build comprehensive security monitoring and threat telemetry dashboards for cyber-hardened Next.js 16 applications.

## Table of Contents

1. [Security Event Logging](#security-event-logging)
2. [Attack Detection](#attack-detection)
3. [Threat Classification](#threat-classification)
4. [CVSS Scoring](#cvss-scoring)
5. [Real-Time Dashboard](#real-time-dashboard)
6. [Alert System](#alert-system)
7. [Analytics & Reporting](#analytics--reporting)
8. [Integration Examples](#integration-examples)

---

## Security Event Logging

### Structured Security Logger

```typescript
// lib/security/logger.ts
import { prisma } from '@/lib/prisma';

export type SecurityEventType = 
  | 'auth_success'
  | 'auth_failure'
  | 'auth_lockout'
  | 'permission_denied'
  | 'sql_injection_attempt'
  | 'xss_attempt'
  | 'csrf_failure'
  | 'rate_limit_exceeded'
  | 'bot_detected'
  | 'suspicious_request'
  | 'brute_force_attempt'
  | 'file_upload_blocked'
  | 'api_abuse'
  | 'session_hijack_attempt';

export type Severity = 'info' | 'low' | 'medium' | 'high' | 'critical';

export interface SecurityEvent {
  type: SecurityEventType;
  severity: Severity;
  ip: string;
  userId?: string;
  userAgent?: string;
  url: string;
  method: string;
  details: Record<string, unknown>;
  blocked: boolean;
}

export async function logSecurityEvent(event: SecurityEvent): Promise<void> {
  const timestamp = new Date();
  
  // Console log for immediate visibility
  const logFn = event.severity === 'critical' || event.severity === 'high' 
    ? console.error 
    : event.severity === 'medium' 
    ? console.warn 
    : console.info;
  
  logFn(`[SECURITY_EVENT] ${event.type}`, {
    ...event,
    timestamp: timestamp.toISOString(),
  });
  
  // Persist to database
  try {
    await prisma.securityEvent.create({
      data: {
        type: event.type,
        severity: event.severity,
        ip: event.ip,
        userId: event.userId,
        userAgent: event.userAgent,
        url: event.url,
        method: event.method,
        details: event.details,
        blocked: event.blocked,
        timestamp,
      },
    });
  } catch (error) {
    console.error('[SECURITY_LOG_ERROR] Failed to persist event:', error);
  }
  
  // Trigger real-time notification for critical events
  if (event.severity === 'critical' || event.severity === 'high') {
    await triggerSecurityAlert(event);
  }
}

// Batch logging for high-volume scenarios
const eventQueue: SecurityEvent[] = [];
let flushTimeout: NodeJS.Timeout | null = null;

export function queueSecurityEvent(event: SecurityEvent): void {
  eventQueue.push(event);
  
  if (!flushTimeout) {
    flushTimeout = setTimeout(flushEventQueue, 1000);
  }
}

async function flushEventQueue(): Promise<void> {
  if (eventQueue.length === 0) {
    flushTimeout = null;
    return;
  }
  
  const events = [...eventQueue];
  eventQueue.length = 0;
  flushTimeout = null;
  
  try {
    await prisma.securityEvent.createMany({
      data: events.map(event => ({
        type: event.type,
        severity: event.severity,
        ip: event.ip,
        userId: event.userId,
        userAgent: event.userAgent,
        url: event.url,
        method: event.method,
        details: event.details,
        blocked: event.blocked,
        timestamp: new Date(),
      })),
    });
  } catch (error) {
    console.error('[SECURITY_LOG_ERROR] Batch insert failed:', error);
  }
}
```

### Request Context Extractor

```typescript
// lib/security/request-context.ts
import { NextRequest } from 'next/server';

export interface RequestContext {
  ip: string;
  userAgent: string;
  url: string;
  method: string;
  referer: string | null;
  country: string | null;
  city: string | null;
  headers: Record<string, string>;
}

export function extractRequestContext(request: NextRequest): RequestContext {
  return {
    ip: request.headers.get('x-forwarded-for')?.split(',')[0] ||
        request.headers.get('x-real-ip') ||
        request.headers.get('cf-connecting-ip') ||
        'unknown',
    userAgent: request.headers.get('user-agent') || 'unknown',
    url: request.nextUrl.pathname,
    method: request.method,
    referer: request.headers.get('referer'),
    country: request.headers.get('cf-ipcountry') ||
             request.headers.get('x-vercel-ip-country'),
    city: request.headers.get('x-vercel-ip-city'),
    headers: Object.fromEntries(request.headers.entries()),
  };
}
```

---

## Attack Detection

### Attack Pattern Detector

```typescript
// lib/security/attack-detector.ts
import { SecurityEventType, Severity, logSecurityEvent } from './logger';
import { RequestContext } from './request-context';

interface AttackPattern {
  name: SecurityEventType;
  severity: Severity;
  patterns: RegExp[];
  description: string;
}

const ATTACK_PATTERNS: AttackPattern[] = [
  {
    name: 'sql_injection_attempt',
    severity: 'critical',
    patterns: [
      /(\bSELECT\b.*\bFROM\b|\bUNION\b.*\bSELECT\b)/i,
      /(\bINSERT\b.*\bINTO\b|\bUPDATE\b.*\bSET\b)/i,
      /(\bDROP\b.*\bTABLE\b|\bDELETE\b.*\bFROM\b)/i,
      /('|").*(\bOR\b|\bAND\b).*('|")\s*=\s*('|")/i,
      /;\s*(DROP|DELETE|UPDATE|INSERT|ALTER)/i,
      /\/\*[\s\S]*?\*\//,
      /--\s/,
    ],
    description: 'SQL injection attack attempt',
  },
  {
    name: 'xss_attempt',
    severity: 'high',
    patterns: [
      /<script[^>]*>[\s\S]*?<\/script>/i,
      /javascript:\s*[^'"]*/i,
      /on\w+\s*=\s*["'][^"']*["']/i,
      /<iframe[^>]*>/i,
      /<object[^>]*>/i,
      /<embed[^>]*>/i,
      /document\.(cookie|location|write)/i,
    ],
    description: 'Cross-site scripting attack attempt',
  },
  {
    name: 'suspicious_request',
    severity: 'medium',
    patterns: [
      /\.\.(\/|\\)/,  // Path traversal
      /%2e%2e/i,      // Encoded path traversal
      /\/etc\/passwd/i,
      /\/proc\/self/i,
      /cmd\.exe|powershell/i,
      /\$\{.*\}/,     // Template injection
    ],
    description: 'Suspicious request pattern detected',
  },
];

export async function detectAttacks(
  context: RequestContext,
  body?: string
): Promise<{
  detected: boolean;
  attacks: Array<{ type: SecurityEventType; severity: Severity }>;
}> {
  const attacks: Array<{ type: SecurityEventType; severity: Severity }> = [];
  const contentToCheck = [
    context.url,
    body || '',
    context.referer || '',
    ...Object.values(context.headers),
  ].join(' ');
  
  for (const pattern of ATTACK_PATTERNS) {
    for (const regex of pattern.patterns) {
      if (regex.test(contentToCheck)) {
        attacks.push({
          type: pattern.name,
          severity: pattern.severity,
        });
        
        // Log the detection
        await logSecurityEvent({
          type: pattern.name,
          severity: pattern.severity,
          ip: context.ip,
          userAgent: context.userAgent,
          url: context.url,
          method: context.method,
          details: {
            pattern: regex.toString(),
            description: pattern.description,
            matchedContent: contentToCheck.substring(0, 200), // Truncate for safety
          },
          blocked: true,
        });
        
        break; // One match per pattern type is enough
      }
    }
  }
  
  return {
    detected: attacks.length > 0,
    attacks,
  };
}
```

### Behavioral Analysis

```typescript
// lib/security/behavioral-analysis.ts
import { Redis } from '@upstash/redis';

const redis = new Redis({
  url: process.env.UPSTASH_REDIS_REST_URL!,
  token: process.env.UPSTASH_REDIS_REST_TOKEN!,
});

interface BehaviorProfile {
  requestCount: number;
  uniqueEndpoints: Set<string>;
  errorRate: number;
  avgResponseTime: number;
  suspiciousPatterns: number;
}

export async function analyzeRequestBehavior(
  ip: string,
  endpoint: string,
  statusCode: number,
  responseTime: number
): Promise<{
  anomalous: boolean;
  score: number;
  reasons: string[];
}> {
  const key = `behavior:${ip}`;
  const windowKey = `behavior:window:${ip}`;
  const now = Date.now();
  
  // Store request in time window
  await redis.zadd(windowKey, { score: now, member: `${endpoint}:${statusCode}:${responseTime}` });
  await redis.zremrangebyscore(windowKey, 0, now - 300000); // 5-minute window
  await redis.expire(windowKey, 600);
  
  // Get all requests in window
  const requests = await redis.zrange(windowKey, 0, -1);
  
  const reasons: string[] = [];
  let anomalyScore = 0;
  
  // Check request volume
  if (requests.length > 100) {
    anomalyScore += 30;
    reasons.push(`High request volume: ${requests.length} in 5 minutes`);
  }
  
  // Check error rate
  const errors = requests.filter(r => {
    const code = parseInt(r.split(':')[1]);
    return code >= 400;
  });
  const errorRate = errors.length / requests.length;
  if (errorRate > 0.3) {
    anomalyScore += 20;
    reasons.push(`High error rate: ${(errorRate * 100).toFixed(1)}%`);
  }
  
  // Check endpoint diversity (scanning behavior)
  const uniqueEndpoints = new Set(requests.map(r => r.split(':')[0]));
  if (uniqueEndpoints.size > 50) {
    anomalyScore += 25;
    reasons.push(`Scanning behavior: ${uniqueEndpoints.size} unique endpoints`);
  }
  
  // Check for 404 scanning
  const notFoundRequests = requests.filter(r => r.includes(':404:'));
  if (notFoundRequests.length > 10) {
    anomalyScore += 25;
    reasons.push(`404 scanning: ${notFoundRequests.length} not found requests`);
  }
  
  return {
    anomalous: anomalyScore >= 50,
    score: anomalyScore,
    reasons,
  };
}
```

---

## Threat Classification

### Threat Intelligence

```typescript
// lib/security/threat-intelligence.ts
import { Severity } from './logger';

export interface ThreatInfo {
  category: string;
  severity: Severity;
  mitreTactic?: string;
  mitreId?: string;
  description: string;
  recommendations: string[];
}

const THREAT_DATABASE: Record<string, ThreatInfo> = {
  sql_injection_attempt: {
    category: 'Injection',
    severity: 'critical',
    mitreTactic: 'Initial Access',
    mitreId: 'T1190',
    description: 'SQL injection attempt to manipulate database queries',
    recommendations: [
      'Review and strengthen input validation',
      'Ensure parameterized queries are used',
      'Consider IP blocking for repeated attempts',
      'Review database access logs',
    ],
  },
  xss_attempt: {
    category: 'Injection',
    severity: 'high',
    mitreTactic: 'Execution',
    mitreId: 'T1059.007',
    description: 'Cross-site scripting attempt to inject malicious scripts',
    recommendations: [
      'Strengthen output encoding',
      'Review Content Security Policy',
      'Audit user input sanitization',
    ],
  },
  brute_force_attempt: {
    category: 'Credential Access',
    severity: 'high',
    mitreTactic: 'Credential Access',
    mitreId: 'T1110',
    description: 'Automated password guessing attack',
    recommendations: [
      'Implement account lockout policy',
      'Enable MFA for affected accounts',
      'Consider CAPTCHA for login',
      'Review password policy strength',
    ],
  },
  bot_detected: {
    category: 'Reconnaissance',
    severity: 'medium',
    mitreTactic: 'Reconnaissance',
    mitreId: 'T1595',
    description: 'Automated bot or crawler detected',
    recommendations: [
      'Verify if bot is legitimate',
      'Update robots.txt if needed',
      'Consider bot management solution',
    ],
  },
  rate_limit_exceeded: {
    category: 'Resource Abuse',
    severity: 'low',
    mitreTactic: 'Impact',
    mitreId: 'T1499',
    description: 'Rate limit exceeded - potential DoS attempt',
    recommendations: [
      'Monitor for DDoS patterns',
      'Review rate limit thresholds',
      'Consider adaptive rate limiting',
    ],
  },
};

export function getThreatInfo(eventType: string): ThreatInfo | null {
  return THREAT_DATABASE[eventType] || null;
}

export function classifyThreat(events: Array<{ type: string; count: number }>): {
  overallSeverity: Severity;
  threatLevel: number;
  summary: string;
} {
  let maxSeverity: Severity = 'info';
  let threatLevel = 0;
  const severityOrder: Severity[] = ['info', 'low', 'medium', 'high', 'critical'];
  
  for (const event of events) {
    const info = getThreatInfo(event.type);
    if (info) {
      const severityIndex = severityOrder.indexOf(info.severity);
      const currentIndex = severityOrder.indexOf(maxSeverity);
      
      if (severityIndex > currentIndex) {
        maxSeverity = info.severity;
      }
      
      threatLevel += severityIndex * event.count;
    }
  }
  
  return {
    overallSeverity: maxSeverity,
    threatLevel: Math.min(100, threatLevel),
    summary: `Detected ${events.length} threat categories with ${maxSeverity} maximum severity`,
  };
}
```

---

## CVSS Scoring

### CVSS Calculator

```typescript
// lib/security/cvss.ts

export interface CVSSMetrics {
  // Base metrics
  attackVector: 'network' | 'adjacent' | 'local' | 'physical';
  attackComplexity: 'low' | 'high';
  privilegesRequired: 'none' | 'low' | 'high';
  userInteraction: 'none' | 'required';
  scope: 'unchanged' | 'changed';
  confidentialityImpact: 'none' | 'low' | 'high';
  integrityImpact: 'none' | 'low' | 'high';
  availabilityImpact: 'none' | 'low' | 'high';
}

const METRIC_VALUES = {
  attackVector: { network: 0.85, adjacent: 0.62, local: 0.55, physical: 0.2 },
  attackComplexity: { low: 0.77, high: 0.44 },
  privilegesRequired: {
    unchanged: { none: 0.85, low: 0.62, high: 0.27 },
    changed: { none: 0.85, low: 0.68, high: 0.5 },
  },
  userInteraction: { none: 0.85, required: 0.62 },
  confidentialityImpact: { none: 0, low: 0.22, high: 0.56 },
  integrityImpact: { none: 0, low: 0.22, high: 0.56 },
  availabilityImpact: { none: 0, low: 0.22, high: 0.56 },
};

export function calculateCVSS(metrics: CVSSMetrics): {
  score: number;
  severity: string;
  vector: string;
} {
  const av = METRIC_VALUES.attackVector[metrics.attackVector];
  const ac = METRIC_VALUES.attackComplexity[metrics.attackComplexity];
  const pr = METRIC_VALUES.privilegesRequired[metrics.scope][metrics.privilegesRequired];
  const ui = METRIC_VALUES.userInteraction[metrics.userInteraction];
  
  const c = METRIC_VALUES.confidentialityImpact[metrics.confidentialityImpact];
  const i = METRIC_VALUES.integrityImpact[metrics.integrityImpact];
  const a = METRIC_VALUES.availabilityImpact[metrics.availabilityImpact];
  
  // Exploitability sub-score
  const exploitability = 8.22 * av * ac * pr * ui;
  
  // Impact sub-score
  const iss = 1 - ((1 - c) * (1 - i) * (1 - a));
  const impact = metrics.scope === 'unchanged'
    ? 6.42 * iss
    : 7.52 * (iss - 0.029) - 3.25 * Math.pow(iss - 0.02, 15);
  
  // Base score
  let score = 0;
  if (impact <= 0) {
    score = 0;
  } else if (metrics.scope === 'unchanged') {
    score = Math.min(10, exploitability + impact);
  } else {
    score = Math.min(10, 1.08 * (exploitability + impact));
  }
  
  // Round up to 1 decimal
  score = Math.ceil(score * 10) / 10;
  
  // Determine severity
  let severity: string;
  if (score === 0) severity = 'None';
  else if (score <= 3.9) severity = 'Low';
  else if (score <= 6.9) severity = 'Medium';
  else if (score <= 8.9) severity = 'High';
  else severity = 'Critical';
  
  // Generate vector string
  const vector = `CVSS:3.1/AV:${metrics.attackVector[0].toUpperCase()}/AC:${metrics.attackComplexity[0].toUpperCase()}/PR:${metrics.privilegesRequired[0].toUpperCase()}/UI:${metrics.userInteraction[0].toUpperCase()}/S:${metrics.scope[0].toUpperCase()}/C:${metrics.confidentialityImpact[0].toUpperCase()}/I:${metrics.integrityImpact[0].toUpperCase()}/A:${metrics.availabilityImpact[0].toUpperCase()}`;
  
  return { score, severity, vector };
}

// Pre-defined CVSS for common attacks
export const COMMON_ATTACK_CVSS: Record<string, CVSSMetrics> = {
  sql_injection: {
    attackVector: 'network',
    attackComplexity: 'low',
    privilegesRequired: 'none',
    userInteraction: 'none',
    scope: 'unchanged',
    confidentialityImpact: 'high',
    integrityImpact: 'high',
    availabilityImpact: 'high',
  },
  xss_stored: {
    attackVector: 'network',
    attackComplexity: 'low',
    privilegesRequired: 'low',
    userInteraction: 'required',
    scope: 'changed',
    confidentialityImpact: 'low',
    integrityImpact: 'low',
    availabilityImpact: 'none',
  },
  brute_force: {
    attackVector: 'network',
    attackComplexity: 'low',
    privilegesRequired: 'none',
    userInteraction: 'none',
    scope: 'unchanged',
    confidentialityImpact: 'high',
    integrityImpact: 'none',
    availabilityImpact: 'none',
  },
};
```

---

## Real-Time Dashboard

### Dashboard API Routes

```typescript
// app/api/security/dashboard/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { auth } from '@/auth';
import { prisma } from '@/lib/prisma';

export async function GET(request: NextRequest) {
  // Require admin authentication
  const session = await auth();
  if (!session?.user || session.user.role !== 'admin') {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }
  
  const { searchParams } = new URL(request.url);
  const timeRange = searchParams.get('range') || '24h';
  
  const since = getTimeRangeDate(timeRange);
  
  // Parallel queries for dashboard data
  const [
    eventCounts,
    severityCounts,
    topAttackers,
    recentEvents,
    blockedRequests,
  ] = await Promise.all([
    // Event counts by type
    prisma.securityEvent.groupBy({
      by: ['type'],
      _count: true,
      where: { timestamp: { gte: since } },
    }),
    
    // Severity distribution
    prisma.securityEvent.groupBy({
      by: ['severity'],
      _count: true,
      where: { timestamp: { gte: since } },
    }),
    
    // Top attackers by IP
    prisma.securityEvent.groupBy({
      by: ['ip'],
      _count: true,
      where: {
        timestamp: { gte: since },
        severity: { in: ['high', 'critical'] },
      },
      orderBy: { _count: { ip: 'desc' } },
      take: 10,
    }),
    
    // Recent events
    prisma.securityEvent.findMany({
      where: { timestamp: { gte: since } },
      orderBy: { timestamp: 'desc' },
      take: 50,
    }),
    
    // Blocked request count
    prisma.securityEvent.count({
      where: {
        timestamp: { gte: since },
        blocked: true,
      },
    }),
  ]);
  
  return NextResponse.json({
    summary: {
      totalEvents: recentEvents.length,
      blockedRequests,
      criticalEvents: severityCounts.find(s => s.severity === 'critical')?._count || 0,
      highEvents: severityCounts.find(s => s.severity === 'high')?._count || 0,
    },
    eventsByType: eventCounts,
    severityDistribution: severityCounts,
    topAttackers,
    recentEvents: recentEvents.slice(0, 20),
    timeRange,
  });
}

function getTimeRangeDate(range: string): Date {
  const now = new Date();
  switch (range) {
    case '1h': return new Date(now.getTime() - 60 * 60 * 1000);
    case '24h': return new Date(now.getTime() - 24 * 60 * 60 * 1000);
    case '7d': return new Date(now.getTime() - 7 * 24 * 60 * 60 * 1000);
    case '30d': return new Date(now.getTime() - 30 * 24 * 60 * 60 * 1000);
    default: return new Date(now.getTime() - 24 * 60 * 60 * 1000);
  }
}
```

### Real-Time Event Stream

```typescript
// app/api/security/events/stream/route.ts
import { NextRequest } from 'next/server';
import { auth } from '@/auth';

export async function GET(request: NextRequest) {
  const session = await auth();
  if (!session?.user || session.user.role !== 'admin') {
    return new Response('Unauthorized', { status: 401 });
  }
  
  const encoder = new TextEncoder();
  
  const stream = new ReadableStream({
    async start(controller) {
      // Send heartbeat every 30 seconds
      const heartbeat = setInterval(() => {
        controller.enqueue(encoder.encode(': heartbeat\n\n'));
      }, 30000);
      
      // Subscribe to security events (using Redis pub/sub or similar)
      const subscription = await subscribeToSecurityEvents((event) => {
        const data = `data: ${JSON.stringify(event)}\n\n`;
        controller.enqueue(encoder.encode(data));
      });
      
      // Cleanup on close
      request.signal.addEventListener('abort', () => {
        clearInterval(heartbeat);
        subscription.unsubscribe();
      });
    },
  });
  
  return new Response(stream, {
    headers: {
      'Content-Type': 'text/event-stream',
      'Cache-Control': 'no-cache',
      'Connection': 'keep-alive',
    },
  });
}

async function subscribeToSecurityEvents(
  callback: (event: unknown) => void
): Promise<{ unsubscribe: () => void }> {
  // Implementation depends on your pub/sub solution
  // Example with Redis:
  // const redis = new Redis(...);
  // await redis.subscribe('security-events');
  // redis.on('message', (channel, message) => callback(JSON.parse(message)));
  
  return { unsubscribe: () => {} };
}
```

### Dashboard Component

```tsx
// components/security/ThreatDashboard.tsx
'use client';

import { useEffect, useState } from 'react';
import useSWR from 'swr';

interface DashboardData {
  summary: {
    totalEvents: number;
    blockedRequests: number;
    criticalEvents: number;
    highEvents: number;
  };
  eventsByType: Array<{ type: string; _count: number }>;
  severityDistribution: Array<{ severity: string; _count: number }>;
  topAttackers: Array<{ ip: string; _count: number }>;
  recentEvents: Array<{
    id: string;
    type: string;
    severity: string;
    ip: string;
    url: string;
    timestamp: string;
  }>;
}

const fetcher = (url: string) => fetch(url).then(res => res.json());

export function ThreatDashboard() {
  const [timeRange, setTimeRange] = useState('24h');
  const { data, error, isLoading } = useSWR<DashboardData>(
    `/api/security/dashboard?range=${timeRange}`,
    fetcher,
    { refreshInterval: 30000 }
  );
  
  if (isLoading) return <div>Loading security dashboard...</div>;
  if (error) return <div>Error loading dashboard</div>;
  if (!data) return null;
  
  return (
    <div className="p-6 space-y-6">
      <div className="flex justify-between items-center">
        <h1 className="text-2xl font-bold">Threat Telemetry Dashboard</h1>
        <select
          value={timeRange}
          onChange={(e) => setTimeRange(e.target.value)}
          className="border rounded px-3 py-2"
        >
          <option value="1h">Last Hour</option>
          <option value="24h">Last 24 Hours</option>
          <option value="7d">Last 7 Days</option>
          <option value="30d">Last 30 Days</option>
        </select>
      </div>
      
      {/* Summary Cards */}
      <div className="grid grid-cols-4 gap-4">
        <SummaryCard
          title="Total Events"
          value={data.summary.totalEvents}
          color="blue"
        />
        <SummaryCard
          title="Blocked Requests"
          value={data.summary.blockedRequests}
          color="green"
        />
        <SummaryCard
          title="Critical Events"
          value={data.summary.criticalEvents}
          color="red"
        />
        <SummaryCard
          title="High Severity"
          value={data.summary.highEvents}
          color="orange"
        />
      </div>
      
      {/* Event Distribution */}
      <div className="grid grid-cols-2 gap-6">
        <div className="bg-white rounded-lg shadow p-4">
          <h2 className="font-semibold mb-4">Events by Type</h2>
          <div className="space-y-2">
            {data.eventsByType.map((item) => (
              <div key={item.type} className="flex justify-between">
                <span className="text-sm">{formatEventType(item.type)}</span>
                <span className="font-medium">{item._count}</span>
              </div>
            ))}
          </div>
        </div>
        
        <div className="bg-white rounded-lg shadow p-4">
          <h2 className="font-semibold mb-4">Top Suspicious IPs</h2>
          <div className="space-y-2">
            {data.topAttackers.map((item) => (
              <div key={item.ip} className="flex justify-between">
                <span className="text-sm font-mono">{item.ip}</span>
                <span className="font-medium text-red-600">{item._count}</span>
              </div>
            ))}
          </div>
        </div>
      </div>
      
      {/* Recent Events Table */}
      <div className="bg-white rounded-lg shadow">
        <h2 className="font-semibold p-4 border-b">Recent Security Events</h2>
        <div className="overflow-x-auto">
          <table className="w-full">
            <thead className="bg-gray-50">
              <tr>
                <th className="px-4 py-2 text-left text-sm">Time</th>
                <th className="px-4 py-2 text-left text-sm">Type</th>
                <th className="px-4 py-2 text-left text-sm">Severity</th>
                <th className="px-4 py-2 text-left text-sm">IP</th>
                <th className="px-4 py-2 text-left text-sm">URL</th>
              </tr>
            </thead>
            <tbody>
              {data.recentEvents.map((event) => (
                <tr key={event.id} className="border-t">
                  <td className="px-4 py-2 text-sm">
                    {new Date(event.timestamp).toLocaleString()}
                  </td>
                  <td className="px-4 py-2 text-sm">
                    {formatEventType(event.type)}
                  </td>
                  <td className="px-4 py-2">
                    <SeverityBadge severity={event.severity} />
                  </td>
                  <td className="px-4 py-2 text-sm font-mono">{event.ip}</td>
                  <td className="px-4 py-2 text-sm truncate max-w-xs">
                    {event.url}
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  );
}

function SummaryCard({ title, value, color }: {
  title: string;
  value: number;
  color: string;
}) {
  const colorClasses = {
    blue: 'bg-blue-50 text-blue-700',
    green: 'bg-green-50 text-green-700',
    red: 'bg-red-50 text-red-700',
    orange: 'bg-orange-50 text-orange-700',
  };
  
  return (
    <div className={`rounded-lg p-4 ${colorClasses[color as keyof typeof colorClasses]}`}>
      <p className="text-sm opacity-80">{title}</p>
      <p className="text-3xl font-bold">{value}</p>
    </div>
  );
}

function SeverityBadge({ severity }: { severity: string }) {
  const colors: Record<string, string> = {
    critical: 'bg-red-100 text-red-800',
    high: 'bg-orange-100 text-orange-800',
    medium: 'bg-yellow-100 text-yellow-800',
    low: 'bg-blue-100 text-blue-800',
    info: 'bg-gray-100 text-gray-800',
  };
  
  return (
    <span className={`px-2 py-1 rounded text-xs font-medium ${colors[severity] || colors.info}`}>
      {severity.toUpperCase()}
    </span>
  );
}

function formatEventType(type: string): string {
  return type.split('_').map(w => w.charAt(0).toUpperCase() + w.slice(1)).join(' ');
}
```

---

## Alert System

### Security Alert Configuration

```typescript
// lib/security/alerts.ts
import { SecurityEvent, Severity } from './logger';

interface AlertChannel {
  name: string;
  enabled: boolean;
  minSeverity: Severity;
  send: (event: SecurityEvent) => Promise<void>;
}

const alertChannels: AlertChannel[] = [
  {
    name: 'email',
    enabled: true,
    minSeverity: 'high',
    send: async (event) => {
      await fetch('/api/alerts/email', {
        method: 'POST',
        body: JSON.stringify(event),
      });
    },
  },
  {
    name: 'slack',
    enabled: !!process.env.SLACK_WEBHOOK_URL,
    minSeverity: 'critical',
    send: async (event) => {
      await fetch(process.env.SLACK_WEBHOOK_URL!, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          text: `🚨 Security Alert: ${event.type}`,
          attachments: [{
            color: event.severity === 'critical' ? 'danger' : 'warning',
            fields: [
              { title: 'Severity', value: event.severity, short: true },
              { title: 'IP', value: event.ip, short: true },
              { title: 'URL', value: event.url },
            ],
          }],
        }),
      });
    },
  },
];

export async function triggerSecurityAlert(event: SecurityEvent): Promise<void> {
  const severityOrder: Severity[] = ['info', 'low', 'medium', 'high', 'critical'];
  const eventSeverityIndex = severityOrder.indexOf(event.severity);
  
  const alertPromises = alertChannels
    .filter(channel => {
      if (!channel.enabled) return false;
      const minIndex = severityOrder.indexOf(channel.minSeverity);
      return eventSeverityIndex >= minIndex;
    })
    .map(channel => channel.send(event).catch(err => {
      console.error(`[ALERT_ERROR] ${channel.name}:`, err);
    }));
  
  await Promise.all(alertPromises);
}
```

---

## Analytics & Reporting

### Security Report Generator

```typescript
// lib/security/reports.ts
import { prisma } from '@/lib/prisma';
import { calculateCVSS, COMMON_ATTACK_CVSS } from './cvss';
import { getThreatInfo, classifyThreat } from './threat-intelligence';

export async function generateSecurityReport(
  startDate: Date,
  endDate: Date
): Promise<{
  period: { start: Date; end: Date };
  summary: {
    totalEvents: number;
    blockedAttacks: number;
    uniqueAttackers: number;
    topThreats: Array<{ type: string; count: number; cvss: number }>;
  };
  riskAssessment: {
    overallRisk: string;
    score: number;
    recommendations: string[];
  };
  attackerAnalysis: Array<{
    ip: string;
    eventCount: number;
    attackTypes: string[];
    firstSeen: Date;
    lastSeen: Date;
  }>;
}> {
  const events = await prisma.securityEvent.findMany({
    where: {
      timestamp: { gte: startDate, lte: endDate },
    },
    orderBy: { timestamp: 'desc' },
  });
  
  // Group by type
  const eventsByType = events.reduce((acc, event) => {
    acc[event.type] = (acc[event.type] || 0) + 1;
    return acc;
  }, {} as Record<string, number>);
  
  // Calculate CVSS for top threats
  const topThreats = Object.entries(eventsByType)
    .map(([type, count]) => {
      const cvssMetrics = COMMON_ATTACK_CVSS[type.replace('_attempt', '')];
      const cvss = cvssMetrics ? calculateCVSS(cvssMetrics).score : 0;
      return { type, count, cvss };
    })
    .sort((a, b) => b.cvss * b.count - a.cvss * a.count)
    .slice(0, 10);
  
  // Unique attackers
  const uniqueIps = new Set(events.map(e => e.ip));
  
  // Attacker analysis
  const attackerMap = new Map<string, {
    events: typeof events;
    types: Set<string>;
  }>();
  
  for (const event of events) {
    const attacker = attackerMap.get(event.ip) || {
      events: [],
      types: new Set<string>(),
    };
    attacker.events.push(event);
    attacker.types.add(event.type);
    attackerMap.set(event.ip, attacker);
  }
  
  const attackerAnalysis = Array.from(attackerMap.entries())
    .map(([ip, data]) => ({
      ip,
      eventCount: data.events.length,
      attackTypes: Array.from(data.types),
      firstSeen: data.events[data.events.length - 1].timestamp,
      lastSeen: data.events[0].timestamp,
    }))
    .sort((a, b) => b.eventCount - a.eventCount)
    .slice(0, 20);
  
  // Risk assessment
  const threatClassification = classifyThreat(
    topThreats.map(t => ({ type: t.type, count: t.count }))
  );
  
  const recommendations: string[] = [];
  for (const threat of topThreats.slice(0, 3)) {
    const info = getThreatInfo(threat.type);
    if (info) {
      recommendations.push(...info.recommendations);
    }
  }
  
  return {
    period: { start: startDate, end: endDate },
    summary: {
      totalEvents: events.length,
      blockedAttacks: events.filter(e => e.blocked).length,
      uniqueAttackers: uniqueIps.size,
      topThreats,
    },
    riskAssessment: {
      overallRisk: threatClassification.overallSeverity,
      score: threatClassification.threatLevel,
      recommendations: [...new Set(recommendations)],
    },
    attackerAnalysis,
  };
}
```

---

## Integration Examples

### Middleware Integration

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { extractRequestContext } from '@/lib/security/request-context';
import { detectAttacks } from '@/lib/security/attack-detector';
import { analyzeRequestBehavior } from '@/lib/security/behavioral-analysis';

export async function middleware(request: NextRequest) {
  const context = extractRequestContext(request);
  
  // Detect known attack patterns
  const attackResult = await detectAttacks(context);
  
  if (attackResult.detected) {
    return NextResponse.json(
      { error: 'Request blocked by security filter' },
      { status: 403 }
    );
  }
  
  // Analyze behavior for anomalies
  const behaviorResult = await analyzeRequestBehavior(
    context.ip,
    context.url,
    200,
    0
  );
  
  if (behaviorResult.anomalous) {
    // Log but don't necessarily block
    console.warn('[ANOMALY_DETECTED]', {
      ip: context.ip,
      score: behaviorResult.score,
      reasons: behaviorResult.reasons,
    });
  }
  
  return NextResponse.next();
}
```

### Prisma Schema

```prisma
model SecurityEvent {
  id        String   @id @default(cuid())
  type      String
  severity  String
  ip        String
  userId    String?
  userAgent String?
  url       String
  method    String
  details   Json
  blocked   Boolean
  timestamp DateTime @default(now())
  
  @@index([timestamp])
  @@index([ip])
  @@index([type])
  @@index([severity])
}
```

### Dependencies

```bash
npm install swr
```

### Environment Variables

```env
UPSTASH_REDIS_REST_URL=your-redis-url
UPSTASH_REDIS_REST_TOKEN=your-redis-token
SLACK_WEBHOOK_URL=your-slack-webhook-url
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lewisperez999) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
