---
name: django-explorer
description: **DJANGO FAST EXPLORER** — Specialized skill for rapid exploration and quick Q&A about juntossomosmais Django/Python applications. USE FOR: understanding project structure; identifying app components; locating specific files and patterns; answering quick questions about code organization; finding models, views, serializers, and consumers; exploring API endpoints and URL patterns; understanding authentication flows and permissions; identifying STOMP/RabbitMQ messaging patterns. PROVIDES: fast responses about code structure; app component relationships; file locations and purposes; quick summaries of functionality; guidance for deeper analysis. JUNTOSSOMOSMAIS FOCUS: Understands StandardModelMixin patterns, django-stomp/django-outbox-pattern messaging, DRF authentication classes, health check patterns, and project-specific conventions from django-template architecture. Use when this capability is needed.
metadata:
  author: niltonfrederico
---

# Django Explorer Skill

*Fast exploration and Q&A for juntossomosmais Django applications*

## Purpose

This skill provides rapid exploration and quick answers about Django/Python applications following juntossomosmais architecture patterns. Perfect for understanding project structure, locating components, and getting oriented in existing codebases.

## Core Capabilities

### Fast Project Discovery

- **App Structure Analysis**: Identifies all Django apps and their purposes
- **Component Location**: Quickly finds models, views, serializers, consumers, and tests
- **URL Pattern Discovery**: Maps out API endpoints and routing
- **Dependency Identification**: Analyzes installed packages and requirements
- **Configuration Overview**: Understands settings, environment variables, and deployment setup

### Architecture Understanding

- **Messaging Patterns**: Identifies STOMP/RabbitMQ usage (django-stomp vs django-outbox-pattern)
- **Authentication Setup**: Maps out DRF authentication classes and permissions
- **Database Configuration**: Understands replica/default routing and connection setup
- **Health Check Systems**: Identifies monitoring and health check implementations
- **Background Tasks**: Locates Django-Q workers and job definitions

### Quick Code Analysis

- **Model Relationships**: Maps entity relationships and StandardModelMixin usage
- **API Endpoint Overview**: Summarizes available endpoints and their purposes  
- **Business Logic Flow**: Traces request/response paths through the application
- **Integration Points**: Identifies external service connections and callbacks
- **Error Handling**: Locates exception handling and logging patterns

## juntossomosmais-Specific Knowledge

### Project Structure Patterns

```
django_project/
├── django_project/
│   ├── apps/               # Django applications
│   │   └── example/        # Individual app structure
│   │       ├── api/        # DRF API views and serializers
│   │       ├── models.py   # StandardModelMixin inheritance
│   │       ├── admin.py    # CustomModelAdminMixin usage
│   │       └── pubsub/     # STOMP consumers
│   ├── support/            # Shared utilities and helpers
│   │   ├── db_router.py    # Database routing logic
│   │   ├── logger.py       # RedactingFilter implementation
│   │   └── healthcheck/    # Custom health check backends
│   ├── settings.py         # Environment-based configuration
│   └── urls.py            # Main URL routing
├── scripts/                # Deployment and utility scripts
├── tests/                  # Test structure mirroring apps/
├── manage.py              # Django management with OTEL instrumentation
├── pyproject.toml         # Poetry dependencies
└── docker-compose.yml     # Development environment
```

### Key Architecture Patterns

#### StandardModelMixin Usage

```python
# All models inherit from StandardModelMixin
class AuditAction(StandardModelMixin):
    # Provides: id (UUID), created_at, updated_at
    user_id = models.CharField(max_length=128)
    action = models.CharField(max_length=128)
    success = models.BooleanField()
```

#### STOMP Messaging Patterns  

```python
# Legacy: django-stomp
from django_stomp.builder import build_publisher

# Newer: django-outbox-pattern
from django_outbox_pattern.models import Published
```

#### DRF Authentication Classes

```python  
# Customer authentication
authentication_classes = (LvJWTAuthentication,)

# Seller authentication  
authentication_classes = (LVJWTSellerAuthentication,)

# Callback authentication
authentication_classes = (TokenAuthentication,)
```

#### Health Check Integration

```python
# Custom health checks for STOMP and database
from django_template.support.healthcheck.checkers import (
    DjangoSTOMPHealthCheck,
    DjangoOutboxPatternHealthCheck,
    CustomDatabaseBackendHealthCheck
)
```

### Configuration Patterns

#### Environment Variable Handling

```python
# Required environment variables
USER_API_HOST = getenv_or_raise_exception("USER_API_HOST")

# Boolean environment variables  
DEBUG = strtobool(os.getenv("DJANGO_DEBUG", "False"))

# Boolean with default
USE_DEBUG_APPS = eval_env_as_boolean("USE_DEBUG_APPS", False)
```

#### Database Configuration

```python
# Primary and replica database setup
DATABASES = {
    "default": {...},  # Write operations
    "replica": {...}   # Read operations (when enabled)
}
DATABASE_ROUTERS = ["django_template.support.db_router.DatabaseRouter"]
```

## Fast Exploration Commands

### Project Overview

- "What Django apps are in this project?"
- "Show me the main API endpoints"
- "What messaging patterns does this use?"
- "How is authentication configured?"
- "What external services does this integrate with?"

### Component Discovery

- "Where are the models defined?"
- "Show me all the DRF serializers"  
- "Find the STOMP consumers"
- "Where are the health checks implemented?"
- "What background tasks are configured?"

### Configuration Analysis

- "What environment variables are required?"
- "How is the database configured?"
- "What's the logging setup?"
- "Show me the middleware stack"
- "What debug tools are available?"

### Architecture Questions

- "How does the request flow work?"
- "What's the database routing strategy?"
- "How are permissions handled?"
- "Where is error handling implemented?"
- "What monitoring is in place?"

## Integration with Other Skills

This skill works as the **entry point** for Django application analysis:

1. **Fast Exploration** - Use this skill for initial discovery and quick questions
2. **Deep Analysis** - Reference `django-analyzer` skill when detailed investigation is needed  
3. **Documentation** - Reference `django-documenter` skill when comprehensive documentation is required
4. **Library Validation** - Reference `library-checker` skill when validating dependencies

## Common Use Cases

### New Developer Onboarding

```
"I'm new to this codebase. Can you give me an overview?"
→ Analyzes app structure, main components, and key patterns
→ Identifies entry points and important files to understand
→ Explains the messaging and authentication patterns used
```

### Bug Investigation Quick Start  

```
"Where would I find the code that handles user authentication?"
→ Locates authentication views, middleware, and configuration
→ Explains the JWT authentication flow
→ Points to related consumers and external integrations
```

### Feature Planning

```
"What patterns should I follow to add a new API endpoint?"
→ Shows existing endpoint patterns and DRF usage
→ Explains serializer and view conventions
→ Identifies URL routing and authentication requirements
```

### Deployment Understanding

```
"How is this application deployed and configured?"
→ Analyzes Dockerfile, docker-compose, and scripts
→ Explains environment variable requirements
→ Shows health check and monitoring setup
```

## Performance & Efficiency

### Search Strategy

- **Semantic Search**: For conceptual queries and pattern identification
- **File Search**: For specific component location (models, views, etc.)
- **Grep Search**: For configuration values and specific code patterns
- **Targeted Reading**: Only reads relevant files based on query context

### Response Format

- **Quick Summaries**: Concise overviews perfect for orientation
- **File Locations**: Precise paths to relevant code
- **Key Insights**: Important patterns and conventions identified
- **Next Steps**: Guidance for deeper investigation when needed

### Integration Efficiency

- **Smart Routing**: Determines when to escalate to deeper analysis skills
- **Context Preservation**: Maintains exploration context for follow-up questions
- **Pattern Recognition**: Leverages juntossomosmais-specific knowledge for faster analysis

---

*"Quick answers, smart navigation - your Django exploration companion"*

---
> Source: [niltonfrederico/my-agents](https://github.com/niltonfrederico/my-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
