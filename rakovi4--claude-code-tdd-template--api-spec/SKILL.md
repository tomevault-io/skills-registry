---
name: api-spec
description: Generate OpenAPI specifications for story endpoints. Use when user wants to create API specs for a story or mentions /api-spec command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Generate API Specification

Generate OpenAPI 3.0.3 specifications for frontend endpoints in a story.

## Usage
```
/api-spec "Story name"
/api-spec 5                    # By MVP story number
/api-spec                      # Interactive selection
```

## MVP Mindset

**Generate the MINIMUM viable API, not the complete API.**

- Prefer fewer endpoints - consolidate where possible
- No health/status endpoints (infrastructure, not features)
- No separate replace endpoints (use PUT on same resource)
- No separate "get current state" if POST response returns it
- When in doubt, leave it out

## Workflow

### Phase 1: Context & Story Selection

1. Read context files:
   - `ProductSpecification/BriefProductDescription.txt`
   - `ProductSpecification/MvpStories.txt`
   - `ProductSpecification/stories/*/mockups/` (UI mockups)
   - `ProductSpecification/stories/*/story-specifics.txt` (if exists)

2. Parse user input to find target story:
   - By name: `/api-spec "Login/Logout"` — match in MvpStories.txt
   - By number: `/api-spec 5` — Story #5 from MvpStories.txt
   - Interactive: `/api-spec` — list stories, ask user

3. Read story specification: `ProductSpecification/stories/NN-story-name/NN_StoryName.md`

### Phase 2: Generate Specifications

Based on story and mockups, identify the **minimum** endpoints needed.

**Create endpoints.md:**

```markdown
# [Story Name] - API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | /api/v1/... | ... |

## Notes

- [1-3 bullets only, if needed]
```

Save as: `ProductSpecification/stories/NN-story-name/endpoints.md`

**Create OpenAPI YAML** for each endpoint:

```yaml
openapi: 3.0.3
info:
  title: [Endpoint Title]
  version: 1.0.0

paths:
  /api/v1/[resource]:
    [method]:
      summary: [Brief description]
      operationId: [operationId]
      tags:
        - [resource-tag]
      requestBody:  # if applicable
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/[RequestSchema]'
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/[ResponseSchema]'
        '400':
          description: Validation error
        '401':
          description: Unauthorized
        '500':
          description: Internal server error

components:
  schemas:
    [RequestSchema]:
      type: object
      required: [required-fields]
      properties:
        [field]:
          type: [type]
    [ResponseSchema]:
      type: object
      properties:
        [field]:
          type: [type]
```

Save as: `ProductSpecification/api-specs/[resource]_[action].yaml`

### Phase 3: Summary

Report:
1. Created files
2. Endpoints generated
3. Any design decisions made

## Design Constraints

- **OpenAPI version**: 3.0.3 (YAML format)
- **API versioning**: `/api/v1/` prefix
- **Naming**: snake_case files, kebab-case URLs
- **RESTful**: Follow REST conventions
- **Minimal responses**: Only essential fields, no error examples in schema

## Notes

- Check if spec already exists before creating
- Cross-reference related stories when endpoints are shared
- See `ProductSpecification/MvpStories.txt` for story numbers

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
