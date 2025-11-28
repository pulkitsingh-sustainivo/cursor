# Add Rate Limiter Configuration

## Input

No input required - this command adds rate limiting to the entire application using @nestjs/throttler.

## Rules

- Rate limiting configuration must use ConfigModule (never use process.env directly)
- Global throttler for entire application
- Local throttlers for sensitive routes
- Dynamic limits must be set based on user roles (optional)
- Use kebab-case for files.
- Use PascalCase for classes.

## Tasks to Perform

1. **Install required dependencies**
   - Install throttler package: `npm install @nestjs/throttler`

2. **Create throttler configuration** (if ConfigModule exists)
   - Create or update `src/config/throttler.config.ts`:
     - Export function that returns throttler configuration using ConfigService
     - Include: ttl (time window in seconds), limit (max requests per window)
     - Use ConfigService.get() for all throttler settings
     - Example:
       ```typescript
       export const throttlerConfig = (configService: ConfigService) => ({
         ttl: configService.get('THROTTLE_TTL') || 60,
         limit: configService.get('THROTTLE_LIMIT') || 10,
       });
       ```

3. **Create throttler module**
   - Create `src/common/throttler/throttler.module.ts` (optional, if custom configuration needed)
   - Or configure directly in app.module.ts

4. **Update app.module.ts**
   - Import `ThrottlerModule` from `@nestjs/throttler`
   - Configure ThrottlerModule:
     ```typescript
     ThrottlerModule.forRootAsync({
       imports: [ConfigModule],
       inject: [ConfigService],
       useFactory: (configService: ConfigService) => throttlerConfig(configService),
     })
     ```
     Or use simple configuration:
     ```typescript
     ThrottlerModule.forRoot([{
       ttl: 60000, // 1 minute
       limit: 10,  // 10 requests per minute
     }])
     ```

5. **Update main.ts**
   - Import `ThrottlerGuard` from `@nestjs/throttler`
   - Apply throttler guard globally (optional, or use per-route):
     ```typescript
     app.useGlobalGuards(new ThrottlerGuard());
     ```
   - Or configure per controller/route basis

6. **Create custom throttler guard** (optional - for role-based limits)
   - Create `src/common/guards/custom-throttler.guard.ts`:
     - Extend `ThrottlerGuard` from `@nestjs/throttler`
     - Override `getTracker()` method for custom tracking (IP, user ID, etc.)
     - Implement role-based rate limits

7. **Configure per-route rate limiting**
   - Use `@Throttle()` decorator on controllers or routes:
     ```typescript
     @Throttle({ default: { limit: 3, ttl: 60000 } })
     @Post('login')
     login() { }
     ```

8. **Update Swagger documentation** (if exists)
   - Add rate limit information to API documentation
   - Document rate limit headers in Swagger responses

9. **Create .env.example** (if not exists)
   - Add throttler configuration:
     ```
     THROTTLE_TTL=60
     THROTTLE_LIMIT=10
     ```

## Rate Limiter Configuration Patterns

### Basic Global Configuration

```typescript
// app.module.ts
import { ThrottlerModule } from '@nestjs/throttler';

@Module({
  imports: [
    ThrottlerModule.forRoot([{
      ttl: 60000,        // Time window: 1 minute (in milliseconds)
      limit: 10,         // Max 10 requests per window
    }]),
  ],
})
export class AppModule {}
```

### Advanced Configuration with ConfigModule

```typescript
// src/config/throttler.config.ts
import { ConfigService } from '@nestjs/config';

export const throttlerConfig = (configService: ConfigService) => ({
  ttl: configService.get('THROTTLE_TTL') || 60,
  limit: configService.get('THROTTLE_LIMIT') || 10,
});

// app.module.ts
ThrottlerModule.forRootAsync({
  imports: [ConfigModule],
  inject: [ConfigService],
  useFactory: throttlerConfig,
})
```

### Custom Throttler Guard (Role-based)

```typescript
// src/common/guards/custom-throttler.guard.ts
import { Injectable, ExecutionContext } from '@nestjs/common';
import { ThrottlerGuard } from '@nestjs/throttler';

@Injectable()
export class CustomThrottlerGuard extends ThrottlerGuard {
  protected getTracker(req: Record<string, any>): string {
    // Use user ID if authenticated, otherwise use IP
    return req.user?.id || req.ip;
  }
}
```

### Per-Route Rate Limiting

```typescript
import { Controller, Post } from '@nestjs/common';
import { Throttle } from '@nestjs/throttler';

@Controller('auth')
export class AuthController {
  @Throttle({ default: { limit: 3, ttl: 60000 } }) // 3 requests per minute
  @Post('login')
  login() {
    // Login endpoint with stricter rate limit
  }
}
```

## Rate Limit Headers

The throttler will automatically add headers to responses:
- `X-RateLimit-Limit` - Maximum number of requests allowed
- `X-RateLimit-Remaining` - Number of requests remaining
- `X-RateLimit-Reset` - Time when the rate limit resets

## Error Response

When rate limit is exceeded:
```json
{
  "statusCode": 429,
  "message": "ThrottlerException: Too Many Requests",
  "error": "Too Many Requests"
}
```

## Usage Examples

1. **Global rate limiting:**
   - Applied automatically to all routes if guard is global

2. **Per-route rate limiting:**
   ```typescript
   @Throttle({ default: { limit: 5, ttl: 60000 } })
   @Post('sensitive-action')
   sensitiveAction() { }
   ```

3. **Skip rate limiting for specific routes:**
   ```typescript
   @SkipThrottle()
   @Get('public-endpoint')
   publicEndpoint() { }
   ```

4. **Multiple rate limiters:**
   ```typescript
   ThrottlerModule.forRoot([{
     name: 'short',
     ttl: 1000,
     limit: 3,
   }, {
     name: 'long',
     ttl: 60000,
     limit: 20,
   }])
   ```

## Output

1. Installed `@nestjs/throttler` package
2. Created or updated throttler configuration using ConfigModule
3. Updated `app.module.ts` with ThrottlerModule configuration
4. Applied global throttler guard (or configured per-route)
5. Optional: Created custom throttler guard for advanced scenarios
6. Optional: Configured per-route rate limits using @Throttle decorator
7. Optional: Created `.env.example` with throttler configuration
8. Rate limiting active on all endpoints (or configured routes)

