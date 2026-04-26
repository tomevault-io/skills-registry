---
name: legacy-system-ai-bridger
description: Use when integrating AI with legacy enterprise systems. Use during solution design. Produces integration patterns, architecture recommendations, and migration strategies.
metadata:
  author: ethical-ai-syndicate
---

# Legacy System AI Bridger

## Overview

Plan and design AI integration with legacy enterprise systems. Identify integration patterns, mitigate risks, and create pragmatic architectures that work within constraints.

**Core principle:** Legacy systems can't be ignored. Design AI integration that works with reality, not against it.

## When to Use

- Adding AI to existing enterprise workflows
- Integrating AI with mainframe or legacy applications
- Planning phased modernization with AI
- Designing data flows from legacy to AI

## Output Format

```yaml
legacy_integration:
  project: "[Project name]"
  date: "[YYYY-MM-DD]"
  
  current_state:
    systems:
      - system: "[System name]"
        type: "[Mainframe | Legacy app | etc.]"
        age: "[Years]"
        technology: "[Tech stack]"
        interfaces: ["[Available interfaces]"]
        constraints: ["[Limitations]"]
        data_format: "[Data formats]"
    
    integration_points:
      - point: "[Where AI needs to connect]"
        current_flow: "[How data/process works today]"
        ai_need: "[What AI needs from here]"
  
  target_state:
    ai_capability: "[What AI will do]"
    integration_pattern: "[Pattern selected]"
    architecture: "[High-level architecture]"
  
  integration_design:
    data_flow:
      - step: "[Step in flow]"
        from: "[Source]"
        to: "[Destination]"
        format: "[Data format]"
        frequency: "[Real-time | Batch | etc.]"
        transformation: "[Any transformation needed]"
    
    interface:
      type: "[API | File | Message | etc.]"
      protocol: "[Protocol details]"
      authentication: "[Auth method]"
      error_handling: "[How errors handled]"
    
    middleware:
      needed: [true | false]
      purpose: "[What it does]"
      options: ["[Technology options]"]
  
  risks:
    - risk: "[Risk]"
      likelihood: "[H/M/L]"
      impact: "[H/M/L]"
      mitigation: "[How to address]"
  
  phasing:
    - phase: 1
      scope: "[What's implemented]"
      dependencies: ["[What must exist]"]
      timeline: "[Duration]"
  
  success_criteria:
    - "[Criterion 1]"
```

## Integration Patterns

### Pattern Selection Guide
| Pattern | Best For | Trade-offs |
|---------|----------|------------|
| **API Wrapper** | Systems with database access | Needs development, adds latency |
| **File-Based** | Batch processes, mainframes | High latency, simple |
| **Message Queue** | Async processing, decoupling | Complexity, infrastructure |
| **Screen Scraping** | No other option | Fragile, slow |
| **Database Tap** | Read-only access needed | Coupling risk, requires access |
| **Sidecar/Proxy** | Intercepting traffic | Deployment complexity |

### API Wrapper Pattern
```yaml
api_wrapper:
  when: "Legacy has accessible database or internal APIs"
  
  architecture:
    legacy_system:
      access: "Database connection or internal API"
    
    wrapper_service:
      function: "Expose modern REST/GraphQL API"
      handles: "Authentication, transformation, caching"
    
    ai_service:
      consumes: "Wrapper API"
  
  considerations:
    - "Don't bypass business logic in legacy app"
    - "Handle legacy system availability"
    - "Consider read vs. write operations"
```

### Message-Based Pattern
```yaml
message_based:
  when: "Need decoupling, async is acceptable"
  
  architecture:
    legacy_system:
      publishes: "Events to message queue"
    
    message_broker:
      technology: "Kafka, RabbitMQ, etc."
      provides: "Buffering, routing, replay"
    
    ai_service:
      subscribes: "Relevant topics"
      publishes: "Results back"
  
  considerations:
    - "Define message schemas clearly"
    - "Handle message ordering if needed"
    - "Plan for replay/reprocessing"
```

### File-Based Pattern
```yaml
file_based:
  when: "Mainframe batch, simple integration OK"
  
  architecture:
    legacy_system:
      exports: "Files on schedule (daily, hourly)"
      format: "CSV, fixed-width, EDI"
    
    file_processing:
      pickup: "S3, SFTP, shared drive"
      transformation: "Parse and validate"
    
    ai_service:
      input: "Transformed data"
      output: "Results file or API"
  
  considerations:
    - "File format versioning"
    - "Error handling and dead letter"
    - "Schedule coordination"
```

## Common Challenges

| Challenge | Symptom | Solution |
|-----------|---------|----------|
| **Data format mismatch** | Legacy uses EBCDIC, fixed-width | Transformation layer |
| **No APIs** | Only batch files available | File integration or screen scraping |
| **Downtime windows** | System unavailable nightly | Design for async operation |
| **Tight coupling** | Changes break integration | Abstraction layer, contract testing |
| **Security constraints** | Can't expose network | Secure gateway, jump server |
| **Documentation gaps** | No one knows how it works | Reverse engineering, careful testing |

## Data Considerations

### Data Quality
```yaml
data_quality:
  from_legacy:
    issues:
      - "Inconsistent formats"
      - "Missing values"
      - "Outdated encoding"
    
    handling:
      - "Validation layer before AI"
      - "Default/fallback values"
      - "Data quality monitoring"
```

### Transformation
```yaml
transformation:
  encoding: "EBCDIC → UTF-8"
  format: "Fixed-width → JSON"
  date: "YYMMDD → ISO 8601"
  nulls: "Spaces → null"
  enrichment: "Code lookups, joins"
```

## Phased Approach

### Typical Phases
```
Phase 1: Read-only integration
├── Extract data from legacy
├── AI operates on extracted data
└── Results consumed outside legacy

Phase 2: Write-back capability
├── AI results formatted for legacy
├── Feedback loop established
└── Error handling robust

Phase 3: Real-time integration
├── Lower latency integration
├── Synchronous where needed
└── Graceful degradation
```

## Checklist

- [ ] Legacy systems documented
- [ ] Integration points identified
- [ ] Pattern selected
- [ ] Data flow designed
- [ ] Transformation requirements defined
- [ ] Risks assessed
- [ ] Phasing planned
- [ ] Success criteria defined

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
