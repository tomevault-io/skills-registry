---
name: arcjet-security-skills
description: Comprehensive Arcjet security integration for Next.js 16. Use when implementing shield WAF protection, rate limiting, bot detection, email validation, and sensitive information detection. Essential for developer-first application security. Use when this capability is needed.
metadata:
  author: lewisperez999
---

# Arcjet Security Skills

Comprehensive security integration patterns using Arcjet for Next.js 16 applications. Arcjet provides developer-first security including WAF protection, rate limiting, bot detection, email validation, and sensitive information detection.

## Table of Contents

1. [Installation & Setup](#installation--setup)
2. [Shield WAF Protection](#shield-waf-protection)
3. [Rate Limiting](#rate-limiting)
4. [Bot Protection](#bot-protection)
5. [Email Validation](#email-validation)
6. [Sensitive Information Detection](#sensitive-information-detection)
7. [Middleware Integration](#middleware-integration)
8. [Combined Rules](#combined-rules)
9. [Testing & Debugging](#testing--debugging)
10. [Best Practices](#best-practices)

---

## Installation & Setup

### Install Dependencies

```bash
npm install @arcjet/next @arcjet/inspect
```

### Environment Configuration

Create a free account at [app.arcjet.com](https://app.arcjet.com) and get your site key.

```env
# .env.local
ARCJET_KEY=ajkey_yourkey
```

### Base Arcjet Configuration

```typescript
// lib/arcjet/client.ts
import arcjet, {
  shield,
  detectBot,
  tokenBucket,
  fixedWindow,
  slidingWindow,
  validateEmail,
  sensitiveInfo,
} from "@arcjet/next";

// Create a base Arcjet client for reuse
export const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  characteristics: ["ip.src"], // Track by IP by default
  rules: [],
});

// Export rule creators for modular use
export { shield, detectBot, tokenBucket, fixedWindow, slidingWindow, validateEmail, sensitiveInfo };
```

---

## Shield WAF Protection

Shield protects against common attacks including OWASP Top 10 vulnerabilities like SQL injection and XSS.

### Basic Shield Protection

```typescript
// app/api/protected/route.ts
import arcjet, { shield } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({
      mode: "LIVE", // Use "DRY_RUN" for testing
    }),
  ],
});

export async function GET(req: Request) {
  const decision = await aj.protect(req);

  // Log all rule results for monitoring
  for (const result of decision.results) {
    console.log("Rule Result", result);
  }

  if (decision.isDenied() && decision.reason.isShield()) {
    return NextResponse.json(
      { error: "Suspicious activity detected" },
      { status: 403 }
    );
  }

  return NextResponse.json({ message: "Request allowed" });
}
```

### Shield with Detailed Logging

```typescript
// app/api/secure/route.ts
import arcjet, { shield } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({
      mode: "LIVE",
    }),
  ],
});

export async function POST(req: Request) {
  const decision = await aj.protect(req);

  // Log security events
  console.log("[ARCJET_SECURITY]", {
    timestamp: new Date().toISOString(),
    conclusion: decision.conclusion,
    ruleResults: decision.results.map(r => ({
      state: r.state,
      conclusion: r.conclusion,
      reason: r.reason,
    })),
  });

  if (decision.isDenied()) {
    // Log attack attempt
    console.warn("[SECURITY_THREAT]", {
      timestamp: new Date().toISOString(),
      reason: decision.reason,
      ip: decision.ip,
    });

    return NextResponse.json(
      { error: "Request blocked" },
      { status: 403 }
    );
  }

  return NextResponse.json({ success: true });
}
```

---

## Rate Limiting

Arcjet supports multiple rate limiting algorithms: Token Bucket, Fixed Window, and Sliding Window.

### Token Bucket Rate Limiting

Best for APIs where you want to allow bursts of requests.

```typescript
// app/api/rate-limited/route.ts
import arcjet, { tokenBucket } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    tokenBucket({
      mode: "LIVE",
      characteristics: ["ip.src"], // Track by IP
      refillRate: 10, // Tokens added per interval
      interval: 60, // Seconds
      capacity: 100, // Maximum bucket size
    }),
  ],
});

export async function GET(req: Request) {
  const decision = await aj.protect(req, { requested: 1 }); // Deduct 1 token

  if (decision.isDenied()) {
    return NextResponse.json(
      { error: "Too many requests", reason: decision.reason },
      {
        status: 429,
        headers: {
          "Retry-After": "60",
        },
      }
    );
  }

  return NextResponse.json({ message: "Request processed" });
}
```

### Fixed Window Rate Limiting

Best for simple, predictable rate limits.

```typescript
// app/api/fixed-rate/route.ts
import arcjet, { fixedWindow } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    fixedWindow({
      mode: "LIVE",
      characteristics: ["ip.src"],
      window: "1m", // 1 minute window
      max: 100, // Max requests per window
    }),
  ],
});

export async function GET(req: Request) {
  const decision = await aj.protect(req);

  if (decision.isDenied() && decision.reason.isRateLimit()) {
    return NextResponse.json(
      { error: "Rate limit exceeded" },
      { status: 429 }
    );
  }

  return NextResponse.json({ data: "Success" });
}
```

### Sliding Window Rate Limiting

Best for smooth rate limiting without burst edges.

```typescript
// app/api/sliding-rate/route.ts
import arcjet, { slidingWindow } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    slidingWindow({
      mode: "LIVE",
      characteristics: ["ip.src"],
      interval: "1m",
      max: 60,
    }),
  ],
});

export async function GET(req: Request) {
  const decision = await aj.protect(req);

  if (decision.isDenied()) {
    return NextResponse.json(
      { error: "Rate limit exceeded" },
      { status: 429 }
    );
  }

  return NextResponse.json({ data: "Success" });
}
```

### User-Based Rate Limiting

Track rate limits by authenticated user instead of IP.

```typescript
// app/api/user-rate/route.ts
import arcjet, { tokenBucket } from "@arcjet/next";
import { NextResponse } from "next/server";
import { auth } from "@/lib/auth";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    tokenBucket({
      mode: "LIVE",
      characteristics: ["userId"], // Custom characteristic
      refillRate: 20,
      interval: 60,
      capacity: 100,
    }),
  ],
});

export async function GET(req: Request) {
  const session = await auth();
  const userId = session?.user?.id || "anonymous";

  const decision = await aj.protect(req, { userId, requested: 1 });

  if (decision.isDenied()) {
    return NextResponse.json(
      { error: "Too many requests" },
      { status: 429 }
    );
  }

  return NextResponse.json({ data: "User data" });
}
```

---

## Bot Protection

Detect and block automated clients while allowing legitimate bots.

### Basic Bot Detection

```typescript
// app/api/no-bots/route.ts
import arcjet, { detectBot } from "@arcjet/next";
import { isSpoofedBot } from "@arcjet/inspect";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    detectBot({
      mode: "LIVE",
      allow: [
        "CATEGORY:SEARCH_ENGINE", // Google, Bing, etc.
        "CATEGORY:MONITOR", // Uptime monitoring
        "CATEGORY:PREVIEW", // Link previews (Slack, Discord)
      ],
    }),
  ],
});

export async function GET(req: Request) {
  const decision = await aj.protect(req);

  if (decision.isDenied() && decision.reason.isBot()) {
    return NextResponse.json(
      { error: "Automated access not allowed" },
      { status: 403 }
    );
  }

  // Additional spoofed bot check (paid feature)
  if (decision.results.some(isSpoofedBot)) {
    return NextResponse.json(
      { error: "Bot verification failed" },
      { status: 403 }
    );
  }

  return NextResponse.json({ message: "Welcome, human!" });
}
```

### Deny Specific Bots

```typescript
// app/api/selective-bots/route.ts
import arcjet, { detectBot } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    detectBot({
      mode: "LIVE",
      // Only deny specific bot categories
      deny: [
        "CATEGORY:AI", // AI scrapers
        "CATEGORY:SCRAPER", // Web scrapers
        "CURL", // curl requests
      ],
    }),
  ],
});

export async function GET(req: Request) {
  const decision = await aj.protect(req);

  if (decision.isDenied()) {
    return NextResponse.json(
      { error: "Access denied" },
      { status: 403 }
    );
  }

  return NextResponse.json({ data: "Protected content" });
}
```

### Form Protection from Bots

```typescript
// app/api/contact/route.ts
import arcjet, { detectBot, shield, tokenBucket } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({ mode: "LIVE" }),
    detectBot({
      mode: "LIVE",
      allow: [], // Block all bots on form submission
    }),
    tokenBucket({
      mode: "LIVE",
      characteristics: ["ip.src"],
      refillRate: 5,
      interval: 60,
      capacity: 10,
    }),
  ],
});

export async function POST(req: Request) {
  const decision = await aj.protect(req);

  if (decision.isDenied()) {
    if (decision.reason.isBot()) {
      return NextResponse.json(
        { error: "Form submission blocked" },
        { status: 403 }
      );
    }
    if (decision.reason.isRateLimit()) {
      return NextResponse.json(
        { error: "Too many submissions" },
        { status: 429 }
      );
    }
    return NextResponse.json(
      { error: "Request blocked" },
      { status: 403 }
    );
  }

  // Process form submission
  const body = await req.json();
  // ... handle form data

  return NextResponse.json({ success: true });
}
```

---

## Email Validation

Validate email addresses to prevent fraudulent signups.

### Basic Email Validation

```typescript
// app/api/signup/route.ts
import arcjet, { validateEmail } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    validateEmail({
      mode: "LIVE",
      deny: [
        "DISPOSABLE", // Temporary email services
        "INVALID", // Invalid email format
        "NO_MX_RECORDS", // No mail server
      ],
    }),
  ],
});

export async function POST(req: Request) {
  const { email } = await req.json();

  const decision = await aj.protect(req, { email });

  if (decision.isDenied()) {
    return NextResponse.json(
      { error: "Invalid email address" },
      { status: 400 }
    );
  }

  // Proceed with registration
  return NextResponse.json({ message: "Registration successful" });
}
```

### Email Validation with Detailed Feedback

```typescript
// app/api/register/route.ts
import arcjet, { validateEmail, shield, detectBot } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({ mode: "LIVE" }),
    detectBot({
      mode: "LIVE",
      allow: [],
    }),
    validateEmail({
      mode: "LIVE",
      deny: ["DISPOSABLE", "INVALID", "NO_MX_RECORDS"],
    }),
  ],
});

export async function POST(req: Request) {
  const body = await req.json();
  const { email, password, name } = body;

  const decision = await aj.protect(req, { email });

  if (decision.isDenied()) {
    // Provide specific feedback based on denial reason
    if (decision.reason.isEmail()) {
      const emailReason = decision.reason;
      
      if (emailReason.emailTypes?.includes("DISPOSABLE")) {
        return NextResponse.json(
          { error: "Disposable email addresses are not allowed" },
          { status: 400 }
        );
      }
      
      if (emailReason.emailTypes?.includes("INVALID")) {
        return NextResponse.json(
          { error: "Please enter a valid email address" },
          { status: 400 }
        );
      }
      
      if (emailReason.emailTypes?.includes("NO_MX_RECORDS")) {
        return NextResponse.json(
          { error: "Email domain does not accept mail" },
          { status: 400 }
        );
      }
    }

    return NextResponse.json(
      { error: "Registration blocked" },
      { status: 403 }
    );
  }

  // Continue with user registration
  return NextResponse.json({ success: true });
}
```

### Server Action Email Validation

```typescript
// app/actions/signup.ts
"use server";

import arcjet, { validateEmail, detectBot } from "@arcjet/next";
import { headers } from "next/headers";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    detectBot({
      mode: "LIVE",
      allow: [],
    }),
    validateEmail({
      mode: "LIVE",
      deny: ["DISPOSABLE", "INVALID", "NO_MX_RECORDS"],
    }),
  ],
});

export async function signupAction(formData: FormData) {
  const email = formData.get("email") as string;

  // Create a request object for Server Actions
  const headersList = await headers();
  const req = {
    headers: headersList,
  };

  const decision = await aj.protect(req as any, { email });

  if (decision.isDenied()) {
    return {
      success: false,
      error: "Invalid email or suspicious activity detected",
    };
  }

  // Process signup
  return { success: true };
}
```

---

## Sensitive Information Detection

Detect and block requests containing sensitive information like PII.

### Block Sensitive Data in Requests

```typescript
// app/api/chat/route.ts
import arcjet, { sensitiveInfo, shield } from "@arcjet/next";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({ mode: "LIVE" }),
    sensitiveInfo({
      mode: "LIVE",
      deny: ["EMAIL", "CREDIT_CARD_NUMBER", "PHONE_NUMBER"],
    }),
  ],
});

export async function POST(req: Request) {
  const { message } = await req.json();

  const decision = await aj.protect(req, {
    sensitiveInfoValue: message,
  });

  if (decision.isDenied() && decision.reason.isSensitiveInfo()) {
    return NextResponse.json(
      { error: "Please do not include sensitive information" },
      { status: 400 }
    );
  }

  // Process message safely
  return NextResponse.json({ response: "Message received" });
}
```

### AI Chatbot Protection

```typescript
// app/api/ai-chat/route.ts
import arcjet, { sensitiveInfo, shield, tokenBucket } from "@arcjet/next";
import { NextResponse } from "next/server";
import { generateText } from "ai";
import { openai } from "@ai-sdk/openai";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({ mode: "LIVE" }),
    tokenBucket({
      mode: "LIVE",
      characteristics: ["ip.src"],
      refillRate: 10,
      interval: 60,
      capacity: 50,
    }),
    sensitiveInfo({
      mode: "LIVE",
      deny: [
        "EMAIL",
        "CREDIT_CARD_NUMBER",
        "PHONE_NUMBER",
        "IP_ADDRESS",
      ],
    }),
  ],
});

export async function POST(req: Request) {
  const { messages } = await req.json();
  const lastMessage = messages[messages.length - 1]?.content || "";

  const decision = await aj.protect(req, {
    sensitiveInfoValue: lastMessage,
    requested: 1,
  });

  if (decision.isDenied()) {
    if (decision.reason.isSensitiveInfo()) {
      return NextResponse.json(
        { error: "Please don't share personal information" },
        { status: 400 }
      );
    }
    if (decision.reason.isRateLimit()) {
      return NextResponse.json(
        { error: "Too many messages. Please wait a moment." },
        { status: 429 }
      );
    }
    return NextResponse.json(
      { error: "Request blocked" },
      { status: 403 }
    );
  }

  // Process AI request
  const { text } = await generateText({
    model: openai("gpt-4"),
    messages,
  });

  return NextResponse.json({ response: text });
}
```

### Custom Sensitive Info Detection

```typescript
// app/api/secure-input/route.ts
import arcjet, { sensitiveInfo } from "@arcjet/next";
import { NextResponse } from "next/server";

// Custom detection function for SSN pattern
function detectSSN(content: string): string[] {
  const ssnPattern = /\b\d{3}-\d{2}-\d{4}\b/g;
  const matches = content.match(ssnPattern);
  return matches || [];
}

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    sensitiveInfo({
      mode: "LIVE",
      deny: ["CREDIT_CARD_NUMBER", "EMAIL"],
      // Custom detection can be added via detect property
    }),
  ],
});

export async function POST(req: Request) {
  const { input } = await req.json();

  // Custom SSN check
  const ssnMatches = detectSSN(input);
  if (ssnMatches.length > 0) {
    return NextResponse.json(
      { error: "SSN detected - please remove sensitive data" },
      { status: 400 }
    );
  }

  const decision = await aj.protect(req, {
    sensitiveInfoValue: input,
  });

  if (decision.isDenied()) {
    return NextResponse.json(
      { error: "Sensitive information detected" },
      { status: 400 }
    );
  }

  return NextResponse.json({ success: true });
}
```

---

## Middleware Integration

Protect all routes with Arcjet middleware.

### Global Protection Middleware

```typescript
// middleware.ts
import arcjet, { shield, detectBot, tokenBucket } from "@arcjet/next";
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    // WAF protection for all routes
    shield({ mode: "LIVE" }),
    // Bot protection
    detectBot({
      mode: "LIVE",
      allow: [
        "CATEGORY:SEARCH_ENGINE",
        "CATEGORY:MONITOR",
        "CATEGORY:PREVIEW",
      ],
    }),
    // Global rate limit
    tokenBucket({
      mode: "LIVE",
      characteristics: ["ip.src"],
      refillRate: 100,
      interval: 60,
      capacity: 500,
    }),
  ],
});

export async function middleware(request: NextRequest) {
  const decision = await aj.protect(request);

  // Log security decisions
  console.log("[ARCJET_MIDDLEWARE]", {
    path: request.nextUrl.pathname,
    conclusion: decision.conclusion,
    ip: decision.ip.address,
  });

  if (decision.isDenied()) {
    if (decision.reason.isRateLimit()) {
      return NextResponse.json(
        { error: "Too many requests" },
        { status: 429 }
      );
    }
    if (decision.reason.isBot()) {
      return NextResponse.json(
        { error: "Bot detected" },
        { status: 403 }
      );
    }
    if (decision.reason.isShield()) {
      return NextResponse.json(
        { error: "Suspicious request" },
        { status: 403 }
      );
    }
    return NextResponse.json(
      { error: "Access denied" },
      { status: 403 }
    );
  }

  // Check for hosting IPs (likely bots)
  if (decision.ip.isHosting()) {
    // Log but don't block - adjust based on use case
    console.warn("[HOSTING_IP]", decision.ip.address);
  }

  return NextResponse.next();
}

export const config = {
  matcher: [
    // Match all paths except static files
    "/((?!_next/static|_next/image|favicon.ico|public/).*)",
  ],
};
```

### Route-Specific Middleware Rules

```typescript
// middleware.ts
import arcjet, { shield, detectBot, tokenBucket } from "@arcjet/next";
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

// Base protection for all routes
const baseAj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({ mode: "LIVE" }),
  ],
});

// Strict protection for API routes
const apiAj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({ mode: "LIVE" }),
    detectBot({
      mode: "LIVE",
      allow: ["CATEGORY:SEARCH_ENGINE"],
    }),
    tokenBucket({
      mode: "LIVE",
      characteristics: ["ip.src"],
      refillRate: 20,
      interval: 60,
      capacity: 100,
    }),
  ],
});

// Very strict for auth routes
const authAj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({ mode: "LIVE" }),
    detectBot({
      mode: "LIVE",
      allow: [], // No bots on auth
    }),
    tokenBucket({
      mode: "LIVE",
      characteristics: ["ip.src"],
      refillRate: 5,
      interval: 60,
      capacity: 20,
    }),
  ],
});

export async function middleware(request: NextRequest) {
  const path = request.nextUrl.pathname;
  
  let aj = baseAj;
  
  if (path.startsWith("/api/auth") || path.startsWith("/login") || path.startsWith("/register")) {
    aj = authAj;
  } else if (path.startsWith("/api/")) {
    aj = apiAj;
  }

  const decision = await aj.protect(request);

  if (decision.isDenied()) {
    const status = decision.reason.isRateLimit() ? 429 : 403;
    return NextResponse.json(
      { error: "Request blocked" },
      { status }
    );
  }

  return NextResponse.next();
}

export const config = {
  matcher: ["/((?!_next/static|_next/image|favicon.ico).*)", "/api/:path*"],
};
```

---

## Combined Rules

Combine multiple Arcjet rules for comprehensive protection.

### Full API Protection

```typescript
// app/api/protected/route.ts
import arcjet, {
  shield,
  detectBot,
  tokenBucket,
  validateEmail,
  sensitiveInfo,
} from "@arcjet/next";
import { isSpoofedBot } from "@arcjet/inspect";
import { NextResponse } from "next/server";

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    // 1. WAF protection
    shield({ mode: "LIVE" }),
    
    // 2. Bot detection
    detectBot({
      mode: "LIVE",
      allow: ["CATEGORY:SEARCH_ENGINE"],
    }),
    
    // 3. Rate limiting
    tokenBucket({
      mode: "LIVE",
      characteristics: ["ip.src"],
      refillRate: 10,
      interval: 60,
      capacity: 50,
    }),
  ],
});

export async function GET(req: Request) {
  const decision = await aj.protect(req, { requested: 1 });
  
  console.log("Arcjet decision:", decision.conclusion);

  if (decision.isDenied()) {
    // Handle different denial reasons
    if (decision.reason.isRateLimit()) {
      return NextResponse.json(
        { error: "Rate limit exceeded" },
        { status: 429 }
      );
    }
    if (decision.reason.isBot()) {
      return NextResponse.json(
        { error: "Automated access blocked" },
        { status: 403 }
      );
    }
    if (decision.reason.isShield()) {
      return NextResponse.json(
        { error: "Suspicious activity" },
        { status: 403 }
      );
    }
    return NextResponse.json(
      { error: "Forbidden" },
      { status: 403 }
    );
  }

  // Additional bot verification check
  if (decision.results.some(isSpoofedBot)) {
    return NextResponse.json(
      { error: "Bot verification failed" },
      { status: 403 }
    );
  }

  // Block hosting IPs if needed
  if (decision.ip.isHosting()) {
    return NextResponse.json(
      { error: "Hosting IPs not allowed" },
      { status: 403 }
    );
  }

  return NextResponse.json({ data: "Protected content" });
}
```

### Registration Endpoint Protection

```typescript
// app/api/register/route.ts
import arcjet, {
  shield,
  detectBot,
  tokenBucket,
  validateEmail,
  sensitiveInfo,
} from "@arcjet/next";
import { NextResponse } from "next/server";
import { z } from "zod";

const registerSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  name: z.string().min(2),
});

const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({ mode: "LIVE" }),
    detectBot({
      mode: "LIVE",
      allow: [], // No bots on registration
    }),
    tokenBucket({
      mode: "LIVE",
      characteristics: ["ip.src"],
      refillRate: 3,
      interval: 60,
      capacity: 10,
    }),
    validateEmail({
      mode: "LIVE",
      deny: ["DISPOSABLE", "INVALID", "NO_MX_RECORDS"],
    }),
  ],
});

export async function POST(req: Request) {
  const body = await req.json();
  
  // Zod validation
  const parseResult = registerSchema.safeParse(body);
  if (!parseResult.success) {
    return NextResponse.json(
      { error: "Invalid input", details: parseResult.error.flatten() },
      { status: 400 }
    );
  }

  const { email, password, name } = parseResult.data;

  // Arcjet protection
  const decision = await aj.protect(req, { email });

  if (decision.isDenied()) {
    if (decision.reason.isEmail()) {
      return NextResponse.json(
        { error: "Please use a valid, non-disposable email" },
        { status: 400 }
      );
    }
    if (decision.reason.isRateLimit()) {
      return NextResponse.json(
        { error: "Too many registration attempts" },
        { status: 429 }
      );
    }
    return NextResponse.json(
      { error: "Registration blocked" },
      { status: 403 }
    );
  }

  // Proceed with user creation
  // ...

  return NextResponse.json({ success: true });
}
```

---

## Testing & Debugging

### DRY_RUN Mode

Use DRY_RUN mode to test rules without blocking requests.

```typescript
// Test configuration
const aj = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({
      mode: process.env.NODE_ENV === "production" ? "LIVE" : "DRY_RUN",
    }),
    detectBot({
      mode: process.env.NODE_ENV === "production" ? "LIVE" : "DRY_RUN",
      allow: ["CATEGORY:SEARCH_ENGINE"],
    }),
  ],
});
```

### Simulating Attacks for Testing

```bash
# Test Shield WAF - simulates suspicious request
curl -v -H "x-arcjet-suspicious: true" http://localhost:3000/api/protected

# After 5 requests with this header, Shield will be triggered

# Test bot detection
curl -v http://localhost:3000/api/protected
# curl is detected as a bot by default

# Test rate limiting - send many requests
for i in {1..20}; do curl http://localhost:3000/api/protected; done
```

### Logging Decisions

```typescript
// lib/arcjet/logging.ts
import type { ArcjetDecision } from "@arcjet/next";

export function logArcjetDecision(
  decision: ArcjetDecision,
  context: { path: string; method: string }
) {
  const log = {
    timestamp: new Date().toISOString(),
    path: context.path,
    method: context.method,
    conclusion: decision.conclusion,
    ip: decision.ip.address,
    ipInfo: {
      isHosting: decision.ip.isHosting(),
      isVpn: decision.ip.isVpn(),
      isProxy: decision.ip.isProxy(),
      isTor: decision.ip.isTor(),
      isRelay: decision.ip.isRelay(),
    },
    results: decision.results.map(r => ({
      ruleId: r.ruleId,
      state: r.state,
      conclusion: r.conclusion,
      reason: r.reason,
    })),
  };

  if (decision.isDenied()) {
    console.warn("[ARCJET_DENIED]", JSON.stringify(log));
  } else {
    console.log("[ARCJET_ALLOWED]", JSON.stringify(log));
  }
}
```

---

## Best Practices

### Security Configuration Checklist

1. **Shield WAF**
   - Enable on all routes handling user input
   - Use LIVE mode in production
   - Monitor dashboard for attack patterns

2. **Rate Limiting**
   - Apply appropriate limits per endpoint type
   - Use token bucket for API endpoints
   - Strict limits on authentication routes

3. **Bot Protection**
   - Allow legitimate bots (search engines, monitors)
   - Block all bots on sensitive endpoints (auth, forms)
   - Use bot verification for additional security

4. **Email Validation**
   - Block disposable emails on registration
   - Require MX records for business applications
   - Combine with rate limiting

5. **Sensitive Info Detection**
   - Enable on all user input endpoints
   - Especially important for AI/chat features
   - Block PII in request bodies

### Environment Variables

```env
# .env.local
ARCJET_KEY=ajkey_yourkey

# Optional: Different keys per environment
ARCJET_KEY_DEV=ajkey_devkey
ARCJET_KEY_PROD=ajkey_prodkey
```

### Dependencies to Install

```bash
npm install @arcjet/next @arcjet/inspect
```

### Integration with Auth.js

```typescript
// lib/arcjet/auth-protection.ts
import arcjet, { shield, detectBot, tokenBucket } from "@arcjet/next";

export const authArcjet = arcjet({
  key: process.env.ARCJET_KEY!,
  rules: [
    shield({ mode: "LIVE" }),
    detectBot({
      mode: "LIVE",
      allow: [], // No bots on auth
    }),
    tokenBucket({
      mode: "LIVE",
      characteristics: ["ip.src"],
      refillRate: 5, // Very conservative for auth
      interval: 300, // 5 minutes
      capacity: 10,
    }),
  ],
});
```

---

## Troubleshooting

### Common Issues

1. **Module not found errors**
   - Ensure @arcjet/next is installed
   - Check that you're using ESM (Arcjet doesn't support CommonJS)

2. **Key not working**
   - Verify ARCJET_KEY in .env.local
   - Check the key format (starts with ajkey_)
   - Ensure the site is properly configured in dashboard

3. **Rules not triggering**
   - Check if mode is "DRY_RUN" (logs only)
   - Verify the rule configuration
   - Check Arcjet dashboard for decision logs

4. **Blocking legitimate users**
   - Review allowed bot categories
   - Adjust rate limits
   - Check IP blocking rules

### Resources

- [Arcjet Documentation](https://docs.arcjet.com)
- [Next.js SDK Reference](https://docs.arcjet.com/reference/nextjs)
- [Bot List](https://arcjet.com/bot-list)
- [Example Apps](https://github.com/arcjet/arcjet-js/tree/main/examples)
- [Arcjet Dashboard](https://app.arcjet.com)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lewisperez999) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
