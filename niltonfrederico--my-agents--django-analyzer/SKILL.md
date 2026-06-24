---
name: django-analyzer
description: **DJANGO DEEP ANALYZER** — Specialized skill for comprehensive, in-depth analysis of juntossomosmais Django/Python applications. USE FOR: detailed code investigation; complex business logic analysis; performance bottleneck identification; security audit of Django patterns; integration flow analysis; database query optimization; STOMP/messaging pattern deep dive; authentication and permission analysis; error handling investigation; test coverage analysis. PROVIDES: detailed code reviews; architectural insights; performance recommendations; security findings; complex relationship mapping; integration flow documentation. JUNTOSSOMOSMAIS FOCUS: Deep understanding of StandardModelMixin patterns, django-stomp vs django-outbox-pattern implementations, DRF authentication flows, health check systems, database routing strategies, and complex business logic patterns from juntossomosmais architecture. Use when this capability is needed.
metadata:
  author: niltonfrederico
---

# Django Deep Analyzer Skill

*Comprehensive analysis and investigation of juntossomosmais Django applications*

## Purpose

This skill provides in-depth analysis of Django/Python applications, going beyond surface exploration to understand complex patterns, relationships, and architectural decisions. Designed to work alongside `django-explorer` when detailed investigation is needed.

## CRITICAL REQUIREMENTS (March 2026 Anti-Hallucination)

### STOP Conditions (MANDATORY)

```python
# When repository context is unclear, STOP and use github-repository-investigator
if not clear_repository_context():
    raise SkillExecutionStop(
        reason="REPOSITORY_CONTEXT_UNCLEAR",
        message="🚫 STOP: Análise requer contexto claro do repositório.\n\n❓ Favor informar qual repositório Django analisar (ex: juntossomosmais/delfos).",
        user_action_required=True
    )

# When required files/apps are missing, STOP and ask user  
if not required_django_files_found():
    raise SkillExecutionStop(
        reason="DJANGO_STRUCTURE_INCOMPLETE",
        message="🚫 STOP: Estrutura Django incompleta ou inacessível.\n\n❓ Este é um projeto Django válido? Verificar manage.py e apps/.",
        user_action_required=True
    )

# When analysis scope is ambiguous, ask user for clarification
if analysis_scope_unclear():
    questions = [{
        "header": "analysis_focus",
        "question": "🔍 Foco da análise Django não está claro. Qual área investigar?",
        "options": [
            {"label": "Padrões de autenticação e permissões", "value": "auth_patterns"},
            {"label": "Fluxos de mensageria (STOMP/Outbox)", "value": "messaging_flows"},
            {"label": "Performance e queries do banco", "value": "performance_analysis"},
            {"label": "Análise arquitetural completa", "value": "full_architecture"}
        ],
        "allowFreeformInput": True
    }]
    user_response = vscode_askQuestions(questions)
    analysis_focus = user_response["analysis_focus"]
```

### MCP-First Pattern (MANDATORY)

- **ALWAYS use mcp_io_github_git_get_file_contents** for repository file access
- **DELEGATE to github-repository-investigator** for repository structure verification
- **NO assumptions** about Django app structure without MCP verification

## Core Capabilities

### Architectural Analysis

- **Complex Pattern Investigation**: Deep dive into messaging, authentication, and data flow patterns
- **Integration Flow Mapping**: Comprehensive analysis of external service integrations
- **Performance Bottleneck Identification**: Database query analysis, N+1 problem detection, caching patterns
- **Security Pattern Review**: Authentication flow security, permission boundaries, data exposure risks
- **Code Quality Assessment**: Design pattern compliance, technical debt identification, maintainability analysis

### Business Logic Deep Dive

- **Transaction Analysis**: Complex database transaction patterns and atomicity
- **Messaging Flow Investigation**: STOMP publisher/consumer relationships and message flow
- **API Endpoint Analysis**: Complex serializer logic, validation chains, and business rules
- **Background Job Investigation**: Django-Q job patterns, scheduling, and error handling
- **Error Handling Analysis**: Exception propagation, logging patterns, and recovery mechanisms

### Data & Relationship Analysis

- **Model Relationship Mapping**: Complex foreign key relationships, through tables, and inheritance
- **Query Optimization Analysis**: Database query patterns, indexing strategies, and performance issues
- **Migration Analysis**: Database schema evolution and migration dependencies
- **Serializer Logic Investigation**: Complex validation rules, transformation logic, and performance
- **Test Coverage Analysis**: Test completeness, edge case coverage, and quality assessment

## juntossomosmais-Specific Deep Analysis

### STOMP Messaging Pattern Analysis

#### Legacy django-stomp Pattern Investigation

```python
# Publisher analysis
from django_stomp.builder import build_publisher

def analyze_publisher_pattern():
    """
    Investigates:
    - Message routing and destination patterns
    - Error handling and retry logic  
    - Transaction boundaries with database operations
    - Header generation and correlation IDs
    """
    publisher = build_publisher()
    # Complex publishing logic analysis
```

#### Modern django-outbox-pattern Investigation  

```python
# Outbox pattern analysis
from django_outbox_pattern.models import Published

def analyze_outbox_pattern():
    """
    Investigates:
    - Outbox table usage and cleanup strategies
    - Message ordering and delivery guarantees
    - Integration with database transactions
    - Failure recovery and dead letter patterns
    """
```

### Authentication Flow Deep Analysis

#### JWT Token Flow Investigation

```python
# Complex authentication analysis
class AuthenticationFlowAnalyzer:
    def analyze_jwt_flow(self):
        """
        Deep analysis of:
        - Token validation and refresh patterns
        - Permission class inheritance and composition
        - Cross-service authentication propagation
        - Security boundary enforcement
        """

    def analyze_seller_vs_customer_auth(self):
        """
        Investigates differences between:
        - LvJWTAuthentication (customer)
        - LVJWTSellerAuthentication (seller)
        - TokenAuthentication (callbacks)
        """
```

### Database Routing Strategy Analysis

#### Complex Query Routing Investigation

```python
# Database router analysis
class DatabaseAnalyzer:
    def analyze_read_write_split(self):
        """
        Investigates:
        - Query routing decisions (replica vs default)
        - Transaction isolation and consistency
        - Connection pooling and performance
        - Migration and schema synchronization
        """

    def analyze_connection_patterns(self):
        """
        Deep dive into:
        - Connection lifecycle management
        - SSL configuration and security
        - Performance tuning parameters
        - Failure handling and fallback strategies
        """
```

### Health Check System Analysis

#### Complex Health Check Investigation

```python
def analyze_health_check_architecture():
    """
    Comprehensive analysis of:
    - Custom health check implementations
    - Integration health vs readiness patterns  
    - Health check dependencies and timeouts
    - Monitoring integration and alerting
    - Circuit breaker patterns in health checks
    """
```

### API Pattern Deep Analysis

#### Complex DRF Pattern Investigation

```python
class APIPatternAnalyzer:
    def analyze_serializer_complexity(self):
        """
        Investigates:
        - Nested serializer relationships
        - Custom validation logic and performance
        - Write vs read serializer patterns
        - Transaction handling in serializers
        """

    def analyze_permission_boundaries(self):
        """
        Deep analysis of:
        - Permission class composition
        - Object-level permission patterns
        - Authentication vs authorization boundaries
        - Security vulnerability assessment
        """
```

## Advanced Analysis Techniques  

### Performance Analysis

- **Query Analysis**: Identifies N+1 queries, missing indexes, and slow queries
- **Memory Usage**: Analyzes object lifecycle and potential memory leaks
- **Caching Patterns**: Evaluates caching effectiveness and invalidation strategies
- **Background Job Performance**: Analyzes job queue patterns and bottlenecks

### Security Analysis

- **Authentication Vulnerabilities**: JWT token validation, session management issues
- **Authorization Boundaries**: Permission bypass possibilities, privilege escalation
- **Data Exposure**: Serializer information leakage, logging sensitive data
- **Input Validation**: SQL injection, XSS, and other injection vulnerabilities

### Integration Analysis

- **External Service Patterns**: HTTP client usage, timeout handling, circuit breakers
- **Message Queue Integration**: STOMP connection management, message reliability
- **Database Integration**: Connection pooling, transaction management, deadlock analysis
- **Monitoring Integration**: OpenTelemetry usage, logging patterns, health checks

## Complex Investigation Workflows

### Business Logic Investigation

```
1. Map the complete request/response flow
2. Analyze all validation layers and business rules
3. Investigate transaction boundaries and rollback scenarios  
4. Review error handling and recovery mechanisms
5. Assess performance implications and optimization opportunities
```

### Integration Flow Analysis

```
1. Trace message flow from producer to consumer
2. Analyze transformation logic and data mapping
3. Investigate error handling and retry mechanisms
4. Review monitoring and observability patterns
5. Assess reliability and failure recovery strategies
```

### Security Deep Dive

```
1. Map authentication and authorization flow
2. Analyze permission boundaries and potential bypasses
3. Review data serialization and potential leakage
4. Investigate input validation and sanitization
5. Assess compliance with security best practices
```

### Performance Investigation

```
1. Analyze database query patterns and optimization
2. Review caching strategies and effectiveness
3. Investigate memory usage and potential leaks
4. Analyze background job patterns and queue health
5. Review monitoring data and performance metrics
```

## Analysis Output Formats

### Comprehensive Reports

- **Architecture Summary**: High-level patterns and design decisions
- **Security Findings**: Vulnerability assessment and recommendations
- **Performance Analysis**: Bottlenecks, optimization opportunities
- **Code Quality Review**: Technical debt, maintainability issues
- **Integration Assessment**: External dependencies and reliability

### Detailed Investigations

- **Code Flow Diagrams**: Step-by-step execution paths
- **Relationship Maps**: Complex entity and service relationships  
- **Performance Profiles**: Query analysis and optimization recommendations
- **Security Audit**: Authentication, authorization, and data protection review
- **Test Coverage Analysis**: Gap identification and quality assessment

### Actionable Recommendations

- **Refactoring Opportunities**: Code improvement suggestions
- **Performance Optimizations**: Specific improvement recommendations
- **Security Enhancements**: Vulnerability fixes and security improvements
- **Architecture Improvements**: Design pattern and structure recommendations
- **Testing Strategy**: Coverage improvements and test quality enhancements

## Integration with Other Skills

### Workflow Integration

1. **django-explorer** - Provides initial discovery and context
2. **django-analyzer** - Performs deep investigation and analysis (this skill)
3. **django-documenter** - Creates comprehensive documentation from analysis
4. **library-checker** - Validates dependencies and compliance

### Collaborative Analysis

- **Performance Focus**: Works with infrastructure analysis for deployment optimization
- **Security Focus**: Integrates with security audit processes and compliance requirements
- **Documentation Focus**: Provides detailed findings for comprehensive documentation
- **Quality Focus**: Supports code review and technical debt reduction efforts

---

*"Uncover the deep truths hidden in your Django architecture"*

---
> Source: [niltonfrederico/my-agents](https://github.com/niltonfrederico/my-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
