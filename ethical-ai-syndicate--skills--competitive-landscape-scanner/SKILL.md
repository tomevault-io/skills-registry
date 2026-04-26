---
name: competitive-landscape-scanner
description: Use when analyzing competitor products and market positioning. Use before roadmap planning or strategy updates. Produces feature comparison, gap analysis, differentiation opportunities, and competitive response recommendations.
metadata:
  author: ethical-ai-syndicate
---

# Competitive Landscape Scanner

## Overview

Systematically analyze the competitive landscape to inform product strategy. Produces structured comparisons, identifies gaps and opportunities, and recommends strategic responses.

**Core principle:** Competitive analysis informs but doesn't dictate strategy. Understand competitors deeply, but differentiate on your unique value, not feature parity.

## When to Use

- Quarterly strategy reviews
- Before major roadmap decisions
- New competitor enters market
- Evaluating build vs buy decisions
- Sales enablement support

## Output Format

```yaml
competitive_analysis:
  date: "[YYYY-MM-DD]"
  market: "[Market/segment analyzed]"
  our_product: "[Product name]"
  
  market_overview:
    total_addressable_market: "[Size]"
    market_growth: "[% annual]"
    key_trends: ["[Trend 1]", "[Trend 2]"]
    buyer_dynamics: "[How decisions are made]"
  
  competitors:
    - name: "[Competitor name]"
      tier: "[Direct | Adjacent | Emerging]"
      positioning: "[Their value proposition]"
      target_segment: "[Who they focus on]"
      pricing_model: "[How they charge]"
      estimated_market_share: "[%]"
      strengths: ["[Strength 1]", "[Strength 2]"]
      weaknesses: ["[Weakness 1]", "[Weakness 2]"]
      recent_moves: ["[Recent development]"]
      threat_level: "[High | Medium | Low]"
  
  feature_comparison:
    categories:
      - category: "[Feature category]"
        features:
          - feature: "[Feature name]"
            importance: "[Critical | Important | Nice-to-have]"
            our_product: "[Strong | Adequate | Weak | Missing]"
            competitor_a: "[Strong | Adequate | Weak | Missing]"
            competitor_b: "[Strong | Adequate | Weak | Missing]"
            gap_opportunity: "[If we're behind or ahead]"
  
  positioning_map:
    axes:
      x: "[Dimension 1, e.g., Price]"
      y: "[Dimension 2, e.g., Complexity]"
    positions:
      - product: "[Name]"
        x_position: "[Low | Medium | High]"
        y_position: "[Low | Medium | High]"
  
  gap_analysis:
    our_advantages:
      - advantage: "[Where we're stronger]"
        sustainability: "[Sustainable | Temporary]"
        leverage_recommendation: "[How to exploit]"
    
    competitor_advantages:
      - advantage: "[Where they're stronger]"
        owner: "[Competitor name]"
        response_options:
          - option: "[Match | Differentiate | Ignore]"
            rationale: "[Why]"
    
    market_gaps:
      - gap: "[Unaddressed market need]"
        addressed_by: "[None | Partial]"
        opportunity_size: "[Large | Medium | Small]"
        our_fit: "[High | Medium | Low]"
  
  strategic_recommendations:
    immediate_actions:
      - action: "[What to do]"
        rationale: "[Why now]"
        competitor_trigger: "[What prompted this]"
    
    positioning_strategy:
      recommended_position: "[How to position against competitors]"
      messaging_focus: "[Key differentiators to emphasize]"
      avoid: ["[What not to compete on]"]
    
    feature_priorities:
      invest: ["[Feature/area to strengthen]"]
      maintain: ["[Feature/area to keep current]"]
      deprioritize: ["[Feature/area to reduce focus]"]
```

## Competitor Tiers

| Tier | Definition | Analysis Depth |
|------|------------|----------------|
| **Direct** | Same problem, same buyer | Deep analysis |
| **Adjacent** | Overlapping functionality | Moderate analysis |
| **Emerging** | New entrants, potential future threat | Watch list |
| **Indirect** | Alternative solutions (incl. status quo) | Light analysis |

## Feature Comparison Framework

### Feature Categories (Example SaaS)
```yaml
feature_categories:
  core_functionality:
    - "[Primary use case feature 1]"
    - "[Primary use case feature 2]"
  
  user_experience:
    - "Onboarding flow"
    - "Mobile support"
    - "Customization options"
  
  integrations:
    - "API availability"
    - "Native integrations"
    - "Webhooks"
  
  enterprise_readiness:
    - "SSO/SAML"
    - "Audit logging"
    - "Role-based permissions"
  
  support_and_success:
    - "Documentation quality"
    - "Support channels"
    - "SLA options"
```

### Scoring Guidelines
| Rating | Meaning | Criteria |
|--------|---------|----------|
| **Strong** | Market-leading | Best-in-class, significant advantage |
| **Adequate** | Meets expectations | Competitive, no major gaps |
| **Weak** | Below expectations | Functional but limited |
| **Missing** | Not available | Feature doesn't exist |

## Positioning Map

### Common Dimensions
| Dimension Pair | Use When |
|----------------|----------|
| Price vs Features | Commodity markets |
| Ease of use vs Power | Complexity tradeoffs |
| Specialization vs Breadth | Vertical vs horizontal |
| Self-service vs High-touch | Go-to-market model |

### Example Map
```
                        Enterprise
                            │
                   ┌────────┼────────┐
                   │   B    │   C    │
      Simple ──────┼────────┼────────┼────── Complex
                   │  US    │   D    │
                   │        │        │
                   └────────┼────────┘
                           SMB

Legend: US = Us, B/C/D = Competitors
```

## Competitive Response Options

### Response Framework
| Situation | Options | Decision Criteria |
|-----------|---------|-------------------|
| Competitor launches feature we lack | Match / Differentiate / Ignore | Strategic importance, customer demand |
| Competitor undercuts pricing | Hold / Segment / Adjust | Value perception, margin impact |
| New market entrant | Monitor / Preempt / Acquire | Threat level, synergy |
| Competitor messaging attacks us | Respond / Ignore / Redirect | Visibility, accuracy of claim |

### When to Match vs Differentiate
```yaml
match_when:
  - "Feature is table stakes (expected by all buyers)"
  - "Significant win/loss impact from gap"
  - "Cost to build is low relative to benefit"

differentiate_when:
  - "Feature isn't aligned with our strategy"
  - "We can solve the underlying need differently"
  - "Copying would dilute our positioning"

ignore_when:
  - "Feature serves segment we don't target"
  - "Low customer demand despite competitor having it"
  - "Distraction from higher-priority work"
```

## Intelligence Gathering

### Primary Sources
| Source | Information Type | Frequency |
|--------|------------------|-----------|
| Competitor websites | Features, messaging, pricing | Monthly |
| G2/Capterra reviews | User sentiment, complaints | Quarterly |
| Job postings | Strategic direction | Quarterly |
| Earnings calls | Strategy, priorities | Quarterly |
| Press releases | Product launches, partnerships | Continuous |
| Win/loss interviews | Competitive dynamics | Continuous |

### Win/Loss Analysis
```yaml
win_loss_summary:
  period: "Q4 2025"
  competitive_deals: 45
  
  wins_against:
    - competitor: "Competitor A"
      wins: 12
      common_reasons:
        - "Better UI/UX" (8)
        - "Pricing" (5)
        - "Integration options" (3)
  
  losses_to:
    - competitor: "Competitor B"
      losses: 8
      common_reasons:
        - "Enterprise features we lack" (5)
        - "Existing relationship" (4)
        - "Brand recognition" (2)
```

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Feature obsession | Chasing parity, not value | Focus on customer outcomes |
| Analysis paralysis | Too much data, no action | Time-box and decide |
| Dismissing competitors | Blind spots | Honest assessment |
| Copycat strategy | No differentiation | Lead with unique value |
| Outdated intel | Stale analysis | Regular refresh cadence |

## Analysis Checklist

- [ ] All tiers of competitors identified
- [ ] Feature comparison is current
- [ ] Positioning map reflects reality
- [ ] Gaps assessed for strategic importance
- [ ] Response recommendations are actionable
- [ ] Win/loss data incorporated
- [ ] Analysis refreshed within last quarter

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
