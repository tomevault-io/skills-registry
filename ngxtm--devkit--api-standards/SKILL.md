---
name: nestjs-api-standards
description: Response wrapping, pagination, and error standardization. Use when this capability is needed.
metadata:
  author: ngxtm
---

# NestJS API Standards & Common Patterns

## Generic Response Wrapper

- **Concept**: Standardize all successful API responses.
- **Implementation**: Use `TransformInterceptor` to wrap data in `{ statusCode, data, meta }`.

## Pagination Standards (Pro)

- **DTOs**: Use strict `PageOptionsDto` (page/take/order) and `PageDto<T>` (data/meta).
- **Swagger Logic**: Generics require `ApiExtraModels` and schema path resolution.
- **Reference**: See [Pagination Wrapper Implementation](references/pagination-wrapper.md) for the complete `ApiPaginatedResponse` decorator code.

## Custom Error Response

- **Standard Error Object**:

  ```typescript
  export class ApiErrorResponse {
    @ApiProperty()
    statusCode: number;

    @ApiProperty()
    message: string;

    @ApiProperty()
    error: string;

    @ApiProperty()
    timestamp: string;

    @ApiProperty()
    path: string;
  }
  ```

- **Docs**: Apply `@ApiBadRequestResponse({ type: ApiErrorResponse })` globally or per controller.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngxtm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
