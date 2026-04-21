---
name: gitlab
description: GitLab DevOps platform. Use for GitLab CI/CD pipelines, version control, issue tracking, merge requests, runners, security scanning, and DevSecOps workflows. Use when this capability is needed.
metadata:
  author: acejou27
---

# GitLab Skill

Comprehensive assistance with GitLab development, operations, and DevOps workflows based on official documentation.

## When to Use This Skill

This skill should be triggered when you need help with:

- **GitLab Runners**: Registering runners, configuring executors (Docker, Kubernetes, Shell), troubleshooting runner issues
- **CI/CD Pipelines**: Creating `.gitlab-ci.yml` files, defining jobs and stages, setting up workflows, validating configuration
- **OAuth & Authentication**: Implementing OAuth 2.0 flows (PKCE, Authorization Code, Device Authorization), managing access tokens
- **Deploy Tokens**: Creating and managing deploy tokens for automated deployments, container registry access, package registry operations
- **API Integration**: Using GitLab GraphQL API, REST API authentication, managing projects and repositories programmatically
- **Container Registry**: Pushing/pulling Docker images, managing registry permissions, using the dependency proxy
- **Package Registry**: Publishing and consuming packages (npm, Maven, NuGet, PyPI), managing package permissions
- **Migration Tasks**: Migrating from Jenkins or CircleCI to GitLab CI/CD
- **GitLab Administration**: Monitoring GitLab instances, reference architectures, scaling strategies

## Quick Reference

### Common Patterns

#### 1. Register a GitLab Runner (Docker)

Register a runner using authentication token (recommended approach):

```bash
# Linux/macOS
gitlab-runner register \
  --non-interactive \
  --url "https://gitlab.com/" \
  --token "$RUNNER_TOKEN" \
  --executor "docker" \
  --docker-image alpine:latest \
  --description "docker-runner"

# Docker container
docker run --rm -v /srv/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner register \
  --non-interactive \
  --url "https://gitlab.com/" \
  --token "$RUNNER_TOKEN" \
  --executor "docker" \
  --docker-image alpine:latest \
  --description "docker-runner"
```

#### 2. Basic GitLab CI/CD Pipeline

Simple pipeline with build, test, and deploy stages:

```yaml
# .gitlab-ci.yml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the application..."
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy.sh
  only:
    - main
```

#### 3. OAuth 2.0 Access Token Request (PKCE)

Secure OAuth flow for public clients (SPAs, mobile apps):

```ruby
# Request access token with PKCE
parameters = 'client_id=APP_ID&code=RETURNED_CODE&grant_type=authorization_code&redirect_uri=REDIRECT_URI&code_verifier=CODE_VERIFIER'
RestClient.post 'https://gitlab.example.com/oauth/token', parameters
```

Response:
```json
{
  "access_token": "de6780bc506a0446309bd9362820ba8aed28aa506c71eedbe1c5c4f9dd350e54",
  "token_type": "bearer",
  "expires_in": 7200,
  "refresh_token": "8257e65c97202ed1726cf9571600918f3bffb2544b26e00a61df9897668c33a1",
  "created_at": 1607635748
}
```

#### 4. Use Deploy Token with Docker Registry

Authenticate and pull/push images using deploy tokens:

```bash
# Login to GitLab Container Registry
echo "$DEPLOY_TOKEN" | docker login -u <username> --password-stdin registry.example.com

# Pull image
docker pull registry.example.com/myproject/myimage:latest

# Push image
docker tag myimage:latest registry.example.com/myproject/myimage:latest
docker push registry.example.com/myproject/myimage:latest
```

#### 5. GitLab CI with Docker Services

Run tests with database services:

```yaml
# .gitlab-ci.yml
test:
  image: ruby:3.1
  services:
    - mysql:latest
    - redis:latest
  variables:
    MYSQL_ROOT_PASSWORD: secret
  script:
    - bundle install
    - bundle exec rspec
```

#### 6. Clone Repository with Deploy Token

```bash
# Clone using deploy token
git clone https://<username>:<deploy_token>@gitlab.example.com/tanuki/awesome_project.git
```

#### 7. GraphQL API Authentication

```bash
# Header-based authentication
curl "https://gitlab.example.com/api/graphql" \
  --header "Authorization: Bearer <access_token>" \
  --header "Content-Type: application/json" \
  --data '{"query": "query { currentUser { name } }"}'

# Parameter-based authentication
curl "https://gitlab.example.com/api/graphql?access_token=<token>" \
  --header "Content-Type: application/json" \
  --data '{"query": "query { currentUser { name } }"}'
```

#### 8. Parallel Jobs in GitLab CI

```yaml
# Jobs run in parallel by default
python_tests:
  image: python:3.9
  script:
    - pytest tests/

java_tests:
  image: openjdk:11
  script:
    - mvn test
  only:
    - staging
```

#### 9. Kubernetes Resource Limits for AI Gateway

```yaml
resources:
  requests:
    memory: "16Gi"
    cpu: "4"
  limits:
    memory: "32Gi"
    cpu: "8"
```

#### 10. Scheduled Pipeline with Rules

```yaml
nightly_build:
  script:
    - ./run_tests.sh
  rules:
    - if: '$CI_PIPELINE_SOURCE == "schedule"'
```

## Key Concepts

### GitLab Runners
**Runners** are agents that execute CI/CD jobs. They can be:
- **Instance runners**: Available to all projects in a GitLab instance
- **Group runners**: Available to all projects in a group
- **Project runners**: Dedicated to specific projects

**Executor types**:
- **Docker**: Runs jobs in Docker containers (most common)
- **Kubernetes**: Runs jobs in Kubernetes pods
- **Shell**: Runs jobs directly on the runner machine
- **Docker-windows**: For Windows containers

### OAuth 2.0 Flows
GitLab supports multiple OAuth flows:
- **Authorization Code with PKCE**: Most secure, recommended for SPAs and mobile apps
- **Authorization Code**: Traditional flow for server-side apps
- **Device Authorization Grant**: For input-constrained devices (headless servers, CLI tools)
- **Resource Owner Password Credentials**: Legacy flow (use personal access tokens instead)

### Deploy Tokens
**Deploy tokens** provide automated access without personal credentials:
- **Scopes**: `read_repository`, `write_repository`, `read_registry`, `write_registry`, `read_package_registry`, `write_package_registry`
- **Special token**: `gitlab-deploy-token` auto-exposes as `CI_DEPLOY_USER` and `CI_DEPLOY_PASSWORD` variables
- **Expiration**: Optional, occurs at midnight UTC on specified date

### CI/CD Stages
Jobs are organized into **stages** that run sequentially:
- Jobs in the same stage run in **parallel**
- Next stage waits for previous stage to complete
- Pipeline fails if any job fails (unless configured otherwise)

### Authentication Methods
- **Personal Access Tokens**: For individual users
- **Project Access Tokens**: For automation within a project
- **Deploy Tokens**: For deployment automation
- **OAuth Tokens**: For third-party applications
- **Session Cookies**: For web UI access

## Reference Files

This skill includes comprehensive documentation in `references/`:

- **administration.md** - GitLab instance administration, monitoring, reference architectures, scaling strategies, and infrastructure requirements
- **api.md** - OAuth 2.0 flows (PKCE, Authorization Code, Device Grant), GraphQL API basics, authentication methods, and token management
- **ci_cd.md** - Runner registration, CI/CD pipeline syntax, job configuration, migration guides (Jenkins, CircleCI), and workflow patterns
- **getting_started.md** - Initial setup, basic concepts, and beginner-friendly tutorials
- **security.md** - Security best practices, token security, runner security, and DevSecOps workflows
- **user_guide.md** - Day-to-day GitLab usage, merge requests, issue tracking, and collaboration features
- **other.md** - Additional topics not covered in other categories

Use these files to find detailed documentation and advanced examples.

## Working with This Skill

### For Beginners

**Start with these topics:**
1. Read `getting_started.md` for foundational concepts
2. Learn about GitLab CI/CD basics in `ci_cd.md`
3. Understand authentication with `api.md`

**First tasks:**
- Create a simple `.gitlab-ci.yml` pipeline
- Register your first runner
- Set up a deploy token for automated deployments

### For CI/CD Engineers

**Focus areas:**
- `ci_cd.md` - Advanced pipeline patterns, caching, artifacts, workflows
- `administration.md` - Runner management, scaling strategies
- Migration guides for Jenkins/CircleCI in `ci_cd.md`

**Common tasks:**
- Configure parallel and sequential job execution
- Set up scheduled pipelines
- Optimize pipeline performance with caching
- Migrate existing CI/CD workflows to GitLab

### For DevOps/Platform Engineers

**Key documentation:**
- `administration.md` - Reference architectures, monitoring, high availability
- `security.md` - Security scanning, vulnerability management
- `api.md` - Automation via GraphQL and REST APIs

**Advanced tasks:**
- Design GitLab infrastructure at scale
- Implement GitLab Geo for disaster recovery
- Configure Kubernetes-based runners
- Set up self-hosted AI Gateway

### For Application Developers

**Relevant sections:**
- Basic CI/CD in `ci_cd.md`
- OAuth integration in `api.md`
- Package/container registry in `user_guide.md`

**Daily workflows:**
- Push code and trigger pipelines
- Create and manage merge requests
- Publish packages to GitLab registries
- Integrate GitLab OAuth into applications

## Navigation Tips

**Need to find specific information?**
- **Runners**: See `ci_cd.md` → "Registering runners"
- **OAuth flows**: See `api.md` → "OAuth 2.0 identity provider API"
- **Deploy tokens**: See `ci_cd.md` or search in reference files
- **Pipeline syntax**: See `ci_cd.md` → "Validate GitLab CI/CD configuration"
- **Migration**: See `ci_cd.md` → "Migrate from Jenkins" or "Migrate from CircleCI"
- **Scaling**: See `administration.md` → "Reference architectures"

**Code examples:**
- Quick Reference section above has the most common patterns
- Each reference file includes code examples from official documentation
- Examples include proper language annotations for syntax highlighting

## Resources

### references/
Organized documentation extracted from official GitLab sources:
- Detailed explanations of features and concepts
- Real-world code examples with language detection
- Links to original official documentation
- Table of contents for quick navigation within each file

### scripts/
Add helper scripts for common GitLab automation tasks:
- Runner setup scripts
- Deployment automation
- API integration helpers

### assets/
Add templates and boilerplate:
- `.gitlab-ci.yml` templates for different tech stacks
- OAuth integration examples
- Kubernetes runner configurations

## Best Practices

### CI/CD Pipelines
- Use `rules:` instead of deprecated `only:`/`except:`
- Cache dependencies to speed up jobs
- Use artifacts to pass data between stages
- Set appropriate timeout values
- Use variables for configuration

### Security
- Never commit tokens or secrets to repositories
- Use CI/CD variables for sensitive data
- Enable protected branches and protected tags
- Use deploy tokens instead of personal credentials
- Regularly rotate tokens and secrets

### Runners
- Use Docker executor for isolation
- Don't run runners in privileged mode unless necessary
- Use tags to target specific runners
- Monitor runner performance and capacity
- Keep runners updated

### OAuth Integration
- Always use PKCE for public clients
- Use HTTPS for redirect URIs in production
- Implement proper state validation for CSRF protection
- Request minimal required scopes
- Handle token refresh properly

## Notes

- This skill was automatically generated from official GitLab documentation
- Reference files preserve structure and examples from source docs
- Code examples include language detection for syntax highlighting
- Quick reference patterns are extracted from common usage examples in the docs

## Updating

To refresh this skill with updated documentation:
1. Re-run the scraper with the same configuration
2. The skill will be rebuilt with the latest information from GitLab docs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acejou27) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
