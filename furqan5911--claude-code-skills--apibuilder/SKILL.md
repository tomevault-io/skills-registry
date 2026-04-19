---
name: apibuilder
description: >- Use when this capability is needed.
metadata:
  author: furqan5911
---

# APIBuilder Skill

## Overview
This skill rapidly generates professional REST APIs with proper documentation, security features, and industry best practices.

## When to Use This Skill
- Rapid prototyping of APIs
- Creating standardized API templates
- Reducing boilerplate code creation time
- Ensuring consistent API structure across projects
- Building APIs with proper documentation and validation
- Implementing secure API patterns

## How to Use
1. User provides API requirements or specifications
2. Generate the complete API structure with endpoints
3. Implement proper request/response validation
4. Add authentication and authorization mechanisms
5. Create comprehensive API documentation
6. Include error handling and logging
7. Generate comprehensive test suite for all endpoints

## API Generation Framework

### Structure Generation
- Create appropriate route structure
- Define request/response models
- Set up database connections/models if needed
- Implement proper HTTP status codes

### Security Implementation
- Add authentication middleware
- Implement rate limiting
- Add input validation and sanitization
- Include CORS configuration

### Documentation Creation
- Generate OpenAPI/Swagger documentation
- Create example requests/responses
- Document all endpoints and parameters
- Include error response definitions

### Best Practices Integration
- Implement proper error handling
- Add logging mechanisms
- Follow RESTful conventions
- Include health check endpoints

## API Testing Framework

### Test Client Generation
- Create API client class with methods for each endpoint
- Include proper authentication headers and parameters
- Implement error handling and response parsing
- Add logging for debugging and monitoring

### Test Cases Structure
- **GET endpoints**: Verify successful retrieval and proper response format
- **POST endpoints**: Test creation with valid/invalid data, check status codes
- **PUT endpoints**: Test updates with various data scenarios
- **DELETE endpoints**: Verify successful deletion and appropriate responses
- **Authentication tests**: Verify access control and security measures
- **Error condition tests**: Test with malformed requests, invalid data, etc.

### Test Configuration
- Environment-based configuration (development, staging, production)
- User authentication and authorization testing
- Integration with CI/CD pipelines
- Performance and load testing capabilities

## Output Format
Generate API code in this structure:
1. **Project Structure**: Directory layout and file organization
2. **Core Files**: Main application file with routes
3. **Models**: Data validation and schema definitions
4. **Middleware**: Authentication, validation, error handling
5. **Documentation**: API documentation and examples
6. **Configuration**: Environment variables and settings
7. **Testing Suite**: API test client and test cases for all endpoints

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/furqan5911) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
