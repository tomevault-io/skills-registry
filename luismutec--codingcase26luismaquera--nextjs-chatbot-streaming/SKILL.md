---
name: nextjs-chatbot-streaming
description: Next.js 14 streaming patterns for real-time chatbot responses with OpenAI SDK. Use when implementing streaming chat, API routes for LLM integration, real-time UI updates, or managing loading states in chat interfaces. Use when this capability is needed.
metadata:
  author: luismutec
---

# Next.js Chatbot Streaming

Streaming implementation patterns for real-time chatbot responses using Next.js 14 App Router and OpenAI SDK.

## When to Apply

Use this skill when:
- Implementing streaming chat responses
- Setting up API routes for LLM integration
- Handling real-time UI updates in chat interfaces
- Managing loading states and progressive rendering
- Optimizing perceived response time

## Key Patterns

### 1. Server-Side Streaming (CRITICAL)

**Pattern:** Use ReadableStream with OpenAI streaming for progressive responses

```typescript
// app/api/chat/route.ts
import { OpenAI } from 'openai'
import { NextRequest, NextResponse } from 'next/server'

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY
})

export async function POST(req: NextRequest) {
  const { message, history } = await req.json()
  
  // Build messages array with system prompt
  const messages = [
    { role: 'system', content: SYSTEM_PROMPT },
    ...history,
    { role: 'user', content: message }
  ]
  
  // Create streaming completion
  const stream = await openai.chat.completions.create({
    model: 'gpt-4o-mini',
    messages,
    stream: true,
    temperature: 0.3
  })
  
  // Create ReadableStream for response
  const encoder = new TextEncoder()
  const readable = new ReadableStream({
    async start(controller) {
      try {
        for await (const chunk of stream) {
          const content = chunk.choices[0]?.delta?.content || ''
          if (content) {
            controller.enqueue(encoder.encode(content))
          }
        }
      } catch (error) {
        controller.error(error)
      } finally {
        controller.close()
      }
    }
  })
  
  return new Response(readable, {
    headers: {
      'Content-Type': 'text/plain; charset=utf-8',
      'Transfer-Encoding': 'chunked'
    }
  })
}
```

### 2. Client-Side Consumption (CRITICAL)

**Pattern:** Use ReadableStreamDefaultReader for progressive UI updates

```typescript
// components/Chat.tsx
'use client'

import { useState } from 'react'

export function Chat() {
  const [messages, setMessages] = useState<Message[]>([])
  const [isLoading, setIsLoading] = useState(false)
  const [error, setError] = useState<string | null>(null)
  
  const sendMessage = async (content: string) => {
    setIsLoading(true)
    setError(null)
    
    // Add user message immediately
    const userMessage: Message = { role: 'user', content }
    setMessages(prev => [...prev, userMessage])
    
    // Add empty assistant message for streaming
    const assistantMessageIndex = messages.length + 1
    setMessages(prev => [...prev, { role: 'assistant', content: '' }])
    
    try {
      const response = await fetch('/api/chat', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          message: content,
          history: messages
        })
      })
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`)
      }
      
      // Stream response
      const reader = response.body!.getReader()
      const decoder = new TextDecoder()
      let accumulatedText = ''
      
      while (true) {
        const { done, value } = await reader.read()
        
        if (done) break
        
        // Decode chunk
        const chunk = decoder.decode(value, { stream: true })
        accumulatedText += chunk
        
        // Update assistant message progressively
        setMessages(prev => {
          const newMessages = [...prev]
          newMessages[assistantMessageIndex] = {
            role: 'assistant',
            content: accumulatedText
          }
          return newMessages
        })
      }
      
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error'
      setError(`Failed to get response: ${errorMessage}`)
      
      // Remove empty assistant message on error
      setMessages(prev => prev.slice(0, -1))
    } finally {
      setIsLoading(false)
    }
  }
  
  return (
    <div>
      {/* Chat UI */}
      {error && <ErrorMessage message={error} />}
      {isLoading && <TypingIndicator />}
    </div>
  )
}
```

### 3. Error Handling (HIGH)

**Pattern:** Graceful fallback with user-friendly error messages

```typescript
// lib/llm/errors.ts
export class LLMError extends Error {
  constructor(
    message: string,
    public code: string,
    public retryable: boolean = false
  ) {
    super(message)
    this.name = 'LLMError'
  }
}

export function handleLLMError(error: unknown): LLMError {
  if (error instanceof OpenAI.APIError) {
    switch (error.status) {
      case 429:
        return new LLMError(
          'Too many requests. Please wait a moment.',
          'RATE_LIMIT',
          true
        )
      case 401:
        return new LLMError(
          'Authentication failed. Please check API key.',
          'AUTH_ERROR',
          false
        )
      case 500:
        return new LLMError(
          'OpenAI service error. Please try again.',
          'SERVICE_ERROR',
          true
        )
      default:
        return new LLMError(
          'Failed to get response. Please try again.',
          'UNKNOWN_ERROR',
          true
        )
    }
  }
  
  return new LLMError(
    'An unexpected error occurred.',
    'UNEXPECTED_ERROR',
    false
  )
}

// Usage in API route
try {
  const stream = await openai.chat.completions.create({...})
  // ... streaming logic
} catch (error) {
  const llmError = handleLLMError(error)
  return NextResponse.json(
    { error: llmError.message, code: llmError.code },
    { status: llmError.retryable ? 503 : 500 }
  )
}
```

### 4. Optimistic UI Updates (MEDIUM)

**Pattern:** Show user message immediately, stream assistant response

```typescript
// components/Chat.tsx
const handleSubmit = async (e: FormEvent) => {
  e.preventDefault()
  
  if (!input.trim() || isLoading) return
  
  const userMessage = input.trim()
  setInput('') // Clear input immediately
  
  // Add user message optimistically
  const optimisticMessage: Message = {
    role: 'user',
    content: userMessage,
    timestamp: new Date()
  }
  
  setMessages(prev => [...prev, optimisticMessage])
  
  // Scroll to bottom
  setTimeout(() => scrollToBottom(), 0)
  
  // Send to API
  await sendMessage(userMessage)
}
```

### 5. Auto-scroll Behavior (MEDIUM)

**Pattern:** Scroll to bottom only when user is near bottom

```typescript
// components/Chat.tsx
import { useRef, useEffect } from 'react'

export function Chat() {
  const messagesEndRef = useRef<HTMLDivElement>(null)
  const scrollContainerRef = useRef<HTMLDivElement>(null)
  const [shouldAutoScroll, setShouldAutoScroll] = useState(true)
  
  // Check if user is near bottom
  const handleScroll = () => {
    const container = scrollContainerRef.current
    if (!container) return
    
    const { scrollTop, scrollHeight, clientHeight } = container
    const distanceFromBottom = scrollHeight - scrollTop - clientHeight
    
    // Auto-scroll if within 100px of bottom
    setShouldAutoScroll(distanceFromBottom < 100)
  }
  
  // Auto-scroll when new messages arrive
  useEffect(() => {
    if (shouldAutoScroll) {
      messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' })
    }
  }, [messages, shouldAutoScroll])
  
  return (
    <div 
      ref={scrollContainerRef}
      onScroll={handleScroll}
      className="overflow-y-auto"
    >
      {messages.map((msg, i) => (
        <MessageBubble key={i} {...msg} />
      ))}
      <div ref={messagesEndRef} />
    </div>
  )
}
```

## Anti-Patterns

### ❌ Don't: Wait for full response before showing anything

```typescript
// BAD: Blocks UI until complete
const response = await fetch('/api/chat')
const fullText = await response.text()
setMessages(prev => [...prev, { role: 'assistant', content: fullText }])
```

### ✅ Do: Stream chunks progressively

```typescript
// GOOD: Progressive updates
const reader = response.body!.getReader()
while (true) {
  const { done, value } = await reader.read()
  if (done) break
  // Update UI with each chunk
  updateMessage(decoder.decode(value))
}
```

### ❌ Don't: Use polling for updates

```typescript
// BAD: Inefficient polling
setInterval(() => {
  fetch('/api/chat/status').then(...)
}, 1000)
```

### ✅ Do: Use native streaming

```typescript
// GOOD: Real-time streaming
const stream = await openai.chat.completions.create({ stream: true })
for await (const chunk of stream) { ... }
```

## Performance Tips

1. **Debounce input** - Prevent accidental double-sends
2. **Limit history** - Send only last 10-20 messages to API
3. **Use AbortController** - Cancel requests on component unmount
4. **Optimize re-renders** - Use React.memo for message bubbles
5. **Batch updates** - Update UI in requestAnimationFrame for smooth streaming

## Testing

```typescript
// Test streaming endpoint
describe('POST /api/chat', () => {
  it('should stream response chunks', async () => {
    const response = await fetch('/api/chat', {
      method: 'POST',
      body: JSON.stringify({ message: 'Hello', history: [] })
    })
    
    expect(response.ok).toBe(true)
    expect(response.headers.get('content-type')).toContain('text/plain')
    
    const reader = response.body!.getReader()
    const { done, value } = await reader.read()
    
    expect(done).toBe(false)
    expect(value).toBeInstanceOf(Uint8Array)
  })
})
```

## References

- [Vercel AI SDK Streaming](https://sdk.vercel.ai/docs/ai-sdk-core/streaming)
- [Next.js Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers)
- [OpenAI Streaming](https://platform.openai.com/docs/api-reference/streaming)
- [MDN ReadableStream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luismutec) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
