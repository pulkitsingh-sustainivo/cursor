# Add Logger Configuration

## Input

No input required - this command configures production-level logging using NestJS Logger.

## Rules

- Logger configuration must be in: `src/main.ts` and throughout the application
- Never use `console.log` - always use NestJS Logger
- Must use NestJS `Logger` from `@nestjs/common`
- Logs must include: timestamp, context, request-id, level
- Use structured logging format

## Tasks to Perform

1. **Update main.ts**
   - Import `Logger` from `@nestjs/common`
   - Create logger instance: `const logger = new Logger('Bootstrap')`
   - Replace any `console.log` with `logger.log()`
   - Add logging for application startup:
     ```typescript
     const port = process.env.PORT ?? 3000;
     await app.listen(port);
     logger.log(`Application is running on: http://localhost:${port}`);
     ```
   - Add error logging for bootstrap failures

2. **Create logging interceptor** (optional but recommended)
   - Create `src/common/interceptors/logging.interceptor.ts`:
     - Implement `NestInterceptor` interface
     - Log incoming requests with method, URL, IP
     - Log outgoing responses with status code, time taken
     - Generate request-id for tracing
     - Use NestJS Logger with context

3. **Create request ID middleware** (optional but recommended)
   - Create `src/common/middleware/request-id.middleware.ts`:
     - Generate unique request ID using UUID
     - Attach request ID to request object
     - Add request ID to response headers (X-Request-Id)

4. **Update existing services to use Logger**
   - Check existing services in `src/cat/services/`
   - Replace any `console.log` with `Logger`
   - Add logger instances to services:
     ```typescript
     private readonly logger = new Logger(CreateCatService.name);
     ```

5. **Update existing controllers to use Logger**
   - Check existing controllers
   - Add logger instances to controllers
   - Log important events (request received, response sent)

6. **Update exception filters to use Logger** (if not already done)
   - Ensure AllExceptionsFilter uses Logger (should already be configured)

7. **Create logger configuration** (optional)
   - Create `src/config/logger.config.ts`:
     - Configure log levels per environment
     - Define log format
     - Export logger configuration

## Logger Implementation Pattern

```typescript
// In services/controllers
import { Logger } from '@nestjs/common';

export class MyService {
  private readonly logger = new Logger(MyService.name);

  someMethod() {
    this.logger.log('Method called');
    this.logger.debug('Debug information');
    this.logger.warn('Warning message');
    this.logger.error('Error message', error.stack);
  }
}
```

## Logging Interceptor Pattern

```typescript
// src/common/interceptors/logging.interceptor.ts
import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  CallHandler,
  Logger,
} from '@nestjs/common';
import { Observable } from 'rxjs';
import { tap } from 'rxjs/operators';

@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  private readonly logger = new Logger(LoggingInterceptor.name);

  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();
    const { method, url, ip } = request;
    const requestId = request.id || 'unknown';

    const now = Date.now();

    this.logger.log(`${method} ${url} - ${ip} [${requestId}]`);

    return next.handle().pipe(
      tap({
        next: () => {
          const response = context.switchToHttp().getResponse();
          const delay = Date.now() - now;
          this.logger.log(
            `${method} ${url} ${response.statusCode} - ${delay}ms [${requestId}]`,
          );
        },
        error: (error) => {
          const delay = Date.now() - now;
          this.logger.error(
            `${method} ${url} ${error.status || 500} - ${delay}ms [${requestId}]`,
            error.stack,
          );
        },
      }),
    );
  }
}
```

## Request ID Middleware Pattern

```typescript
// src/common/middleware/request-id.middleware.ts
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';
import { randomUUID } from 'crypto';

@Injectable()
export class RequestIdMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    const requestId = randomUUID();
    req.id = requestId;
    res.setHeader('X-Request-Id', requestId);
    next();
  }
}
```

## Log Levels

- `logger.log()` - General information (INFO level)
- `logger.debug()` - Debug information (DEBUG level)
- `logger.warn()` - Warning messages (WARN level)
- `logger.error()` - Error messages (ERROR level)
- `logger.verbose()` - Verbose information (VERBOSE level)

## Logger Configuration Example

```typescript
// In main.ts
import { Logger, ValidationPipe } from '@nestjs/common';

async function bootstrap() {
  const logger = new Logger('Bootstrap');

  try {
    const app = await NestFactory.create(AppModule);

    // ... other configurations

    const port = process.env.PORT ?? 3000;
    await app.listen(port);

    logger.log(`Application is running on: http://localhost:${port}`);
    logger.log(`Environment: ${process.env.NODE_ENV || 'development'}`);
  } catch (error) {
    logger.error('Error starting application', error.stack);
    process.exit(1);
  }
}
```

## Output

1. Updated `src/main.ts` to use NestJS Logger instead of console.log
2. Created logger instances throughout the application
3. Added logging for application startup and shutdown
4. Optional: Created `src/common/interceptors/logging.interceptor.ts` for request/response logging
5. Optional: Created `src/common/middleware/request-id.middleware.ts` for request tracing
6. Updated existing services to use Logger
7. Updated existing controllers to use Logger
8. All logging now includes: timestamp, context, level
9. Production-ready logging configuration

