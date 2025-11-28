# Add Exception Filter Configuration

## Input

No input required - this command adds a global exception filter to the entire application.

## Rules

- Create exception filter in: `src/common/filters/`
- Use kebab-case for files.
- Use PascalCase for classes.
- Use camelCase for variables.
- The global exception filter must handle all types of exceptions.
- Always return a consistent error shape.
- Never use `console.log` - use NestJS Logger instead.

## Folder Structure to Generate

src/common/filters/
├── http-exception.filter.ts
└── all-exceptions.filter.ts

## Tasks to Perform

1. **Create folder structure**
   - Create `src/common/filters/` directory if it doesn't exist

2. **Create HttpException Filter** (`http-exception.filter.ts`)
   - Create class `HttpExceptionFilter`
   - Implement `ExceptionFilter` interface
   - Handle `HttpException` specifically
   - Return consistent error response format:
     ```typescript
     {
       statusCode: number,
       timestamp: string,
       path: string,
       message: string | string[]
     }
     ```
   - Use NestJS Logger for error logging

3. **Create All Exceptions Filter** (`all-exceptions.filter.ts`)
   - Create class `AllExceptionsFilter`
   - Implement `ExceptionFilter` interface
   - Handle multiple exception types:
     - `HttpException` (4xx, 5xx status codes)
     - `ValidationException` / `BadRequestException` (validation errors)
     - Database errors (TypeORM/Prisma errors)
     - Uncaught exceptions
   - Return consistent error response format
   - Log errors with context using NestJS Logger
   - Differentiate between production and development (hide stack traces in production)

4. **Update main.ts**
   - Import `AllExceptionsFilter` from `src/common/filters/all-exceptions.filter`
   - Apply filter globally: `app.useGlobalFilters(new AllExceptionsFilter())`
   - Place this before `app.listen()`

5. **Install required dependencies** (if not already installed)
   - Check if packages are installed, if not install:
     - `@nestjs/common` (should already be installed)
     - `class-validator` (should already be installed for validation errors)

## Error Response Format

All exceptions must return this consistent shape:

```typescript
{
  statusCode: number,
  timestamp: string,        // ISO 8601 format
  path: string,             // Request URL path
  message: string | string[], // Error message(s)
  error?: string            // Error type (optional, only in development)
}
```

## Implementation Details

- For `HttpException`: Extract status code and message
- For `ValidationException` / `BadRequestException`: Format validation errors as array
- For Database errors: Map to appropriate HTTP status (500 or 400)
- For Unknown errors: Return 500 with generic message in production
- In development: Include stack trace in response
- In production: Hide stack traces and sensitive information
- All errors must be logged with context using NestJS Logger

## Output

1. Created `src/common/filters/http-exception.filter.ts` with `HttpExceptionFilter` class
2. Created `src/common/filters/all-exceptions.filter.ts` with `AllExceptionsFilter` class
3. Updated `src/main.ts` to use global exception filter
4. Applied exception filter globally to the entire application
5. Consistent error response format across all endpoints

