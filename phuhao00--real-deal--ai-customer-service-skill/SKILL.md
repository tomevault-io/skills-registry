---
name: ai-customer-service-skill
description: AI customer service and recommendation system for real_deal platform including AI chatbot, intelligent recommendations, natural language processing, and user intent understanding. Use when building AI chat interfaces, implementing recommendation engines, processing user queries with NLP, or creating AI-powered features. Use when this capability is needed.
metadata:
  author: phuhao00
---

# AI Customer Service & Recommendations

## AI Customer Service

### Capabilities
- Natural language query understanding
- Context-aware conversations
- Multi-turn dialogues
- Knowledge base integration
- Escalation to human agents

### Use Cases
- Answer platform FAQs
- Guide new users through onboarding
- Help with billing/quotas
- Explain verification processes
- Troubleshoot common issues

### Conversation Management
- Session state tracking in Redis
- Conversation history storage in MongoDB
- Context window management
- Fallback to human support

### Integration Points
- Platform documentation
- Billing system
- Verification workflows
- Account management
- Company/job search

## Intelligent Recommendations

### Recommendation Types

#### Job/Company Recommendations
- Based on user profile and preferences
- Industry, location, role matching
- Historical interaction analysis
- Explainable reasoning

#### Investor-Founder Matching
- Investment criteria alignment
- Stage and industry fit
- Geographic preferences
- Past deal history

#### Content Discovery
- Posts relevant to user interests
- Companies in focus areas
- Projects matching skills
- Products for potential collaboration

### Recommendation Engine
```go
// Generate recommendations
func RecommendJobs(userID string) []Job {
    profile := GetUserProfile(userID)
    preferences := GetUserPreferences(userID)
    history := GetUserHistory(userID)

    candidates := MatchJobs(profile, preferences)
    scored := ScoreCandidates(candidates, history)
    ranked := RankByRelevance(scored)

    return ranked[:10]
}
```

## NLP Processing

### Query Understanding
- Intent classification
- Entity extraction
- Query expansion
- Disambiguation

### Response Generation
- Template-based responses
- Contextual information insertion
- Dynamic content rendering
- Multi-language support

### Language Support
- English (primary)
- Chinese (Simplified)
- Extensible to other languages

## Data Models

### AI System
- `Conversation` - Chat session records
- `Message` - Individual messages (user + AI)
- `Recommendation` - Generated recommendations
- `Feedback` - User feedback on AI responses

### User Tracking
- `UserPreferences` - Preference data
- `InteractionHistory` - Clicks, views, actions
- `RecommendationImpressions` - Shown recommendations

## Common Tasks

### Implement AI Chat
1. Create chat component in frontend
2. WebSocket or polling for real-time
3. Backend endpoint for message processing
4. NLP intent classification
5. Response generation logic
6. Store conversation history

### Build Recommendation System
1. Define recommendation criteria
2. Implement matching algorithm
3. Score and rank candidates
4. Add explainability (why this match?)
5. A/B test recommendation quality
6. Track user engagement

### Integrate Knowledge Base
1. Structure platform documentation
2. Index for semantic search
3. Map queries to answers
4. Update answers as platform evolves
5. Track query patterns for gaps

### Handle Escalation
1. Detect complex/ambiguous queries
2. Route to human support
3. Share conversation context
4. Track resolution time
5. Learn from escalations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuhao00) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
