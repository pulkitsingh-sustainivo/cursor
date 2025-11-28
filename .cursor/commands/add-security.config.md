# Add Security Configuration (Helmet & CORS)

## Input

No input required - this command adds security middleware (Helmet and CORS) to the entire application.

## Rules

- Security configuration must be in: `src/main.ts`
- Use Helmet for HTTP security headers
- CORS whitelist per environment (use ConfigModule if available)
- Enable CSRF for web form submissions (optional)
- Disable stack traces in production
- Use kebab-case for files.
- Use PascalCase for classes.

## Tasks to Perform

1. **Install required dependencies**
   - Install Helmet: `npm install helmet`
   - Install types: `npm install -D @types/helmet`

2. **Create security configuration** (if ConfigModule exists)
   - Create `src/config/security.config.ts`:
     - Export CORS configuration function using ConfigService
     - Define allowed origins per environment
     - Define CORS options (credentials, methods, headers)
     - Example:
       ```typescript
       export const corsConfig = (configService: ConfigService) => ({
         origin: configService.get('CORS_ORIGIN')?.split(',') || ['http://localhost:3000'],
         credentials: true,
         methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH', 'OPTIONS'],
         allowedHeaders: ['Content-Type', 'Authorization'],
       });
       ```

3. **Update main.ts**
   - Import `helmet` from 'helmet'
   - Import `NestFactory` options if needed
   - Apply Helmet middleware: `app.use(helmet())`
   - Configure CORS: `app.enableCors(corsOptions)`
   - Place security middleware before other middleware

4. **Configure Helmet options** (optional)
   - Customize Helmet configuration for specific security needs:
     ```typescript
     app.use(helmet({
       contentSecurityPolicy: {
         directives: {
           defaultSrc: ["'self'"],
           styleSrc: ["'self'", "'unsafe-inline'"],
           scriptSrc: ["'self'"],
           imgSrc: ["'self'", "data:", "https:"],
         },
       },
       crossOriginEmbedderPolicy: false, // Adjust based on needs
     }));
     ```

5. **Configure CORS per environment**
   - Development: Allow all origins (for development ease)
   - Production: Whitelist specific origins
   - Use environment variables for CORS origins

6. **Disable stack traces in production**
   - Ensure exception filters hide stack traces (should already be done)
   - Verify `NODE_ENV=production` hides sensitive information

7. **Optional: Add CSRF protection** (if needed for web forms)
   - Install: `npm install csurf`
   - Install types: `npm install -D @types/csurf`
   - Create CSRF middleware or guard
   - Apply to routes that handle form submissions

8. **Create .env.example** (if not exists)
   - Add security configuration:
     ```
     CORS_ORIGIN=http://localhost:3000,http://localhost:3001
     NODE_ENV=development
     ```

## Helmet Configuration

Helmet sets various HTTP headers to help protect the application from well-known web vulnerabilities:

- `X-Content-Type-Options`: Prevents MIME type sniffing
- `X-Frame-Options`: Prevents clickjacking
- `X-XSS-Protection`: Enables XSS filter
- `Strict-Transport-Security`: Forces HTTPS
- `Content-Security-Policy`: Prevents XSS attacks
- And more...

## CORS Configuration Pattern

### Basic CORS Configuration

```typescript
// main.ts
app.enableCors({
  origin: 'http://localhost:3000',
  credentials: true,
});
```

### Advanced CORS Configuration with ConfigModule

```typescript
// src/config/security.config.ts
import { ConfigService } from '@nestjs/config';

export const corsConfig = (configService: ConfigService) => {
  const origins = configService.get('CORS_ORIGIN')?.split(',') || ['http://localhost:3000'];
  const isProduction = configService.get('NODE_ENV') === 'production';

  return {
    origin: (origin, callback) => {
      // Allow requests with no origin (mobile apps, Postman, etc.)
      if (!origin) return callback(null, true);

      if (origins.indexOf(origin) !== -1 || !isProduction) {
        callback(null, true);
      } else {
        callback(new Error('Not allowed by CORS'));
      }
    },
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH', 'OPTIONS'],
    allowedHeaders: ['Content-Type', 'Authorization', 'X-Request-Id'],
    exposedHeaders: ['X-Request-Id'],
  };
};

// main.ts
const corsOptions = corsConfig(app.get(ConfigService));
app.enableCors(corsOptions);
```

### Environment-based CORS

```typescript
const isProduction = process.env.NODE_ENV === 'production';

app.enableCors({
  origin: isProduction
    ? ['https://yourdomain.com', 'https://www.yourdomain.com']
    : true, // Allow all origins in development
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization'],
});
```

## Complete Security Configuration Example

```typescript
// main.ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import helmet from 'helmet';
import { ConfigService } from '@nestjs/config';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  const configService = app.get(ConfigService);

  // Apply Helmet for security headers
  app.use(helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
        scriptSrc: ["'self'"],
        imgSrc: ["'self'", "data:", "https:"],
      },
    },
    crossOriginEmbedderPolicy: false,
  }));

  // Configure CORS
  const corsOrigins = configService.get('CORS_ORIGIN')?.split(',') || ['http://localhost:3000'];
  const isProduction = configService.get('NODE_ENV') === 'production';

  app.enableCors({
    origin: (origin, callback) => {
      if (!origin || !isProduction || corsOrigins.includes(origin)) {
        callback(null, true);
      } else {
        callback(new Error('Not allowed by CORS'));
      }
    },
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH', 'OPTIONS'],
    allowedHeaders: ['Content-Type', 'Authorization', 'X-Request-Id'],
    exposedHeaders: ['X-Request-Id'],
  });

  // ... other configurations

  await app.listen(process.env.PORT ?? 3000);
}
```

## Security Best Practices

1. **Helmet**: Always enabled, customized per application needs
2. **CORS**: Whitelist specific origins in production
3. **Environment Variables**: Never expose sensitive values
4. **Stack Traces**: Hidden in production
5. **HTTPS**: Use in production (configured via Helmet HSTS)
6. **Content Security Policy**: Configure based on application needs

## Optional: CSRF Protection

If you need CSRF protection for web forms:

```typescript
import * as csurf from 'csurf';

// Apply CSRF protection (skip for API-only applications)
app.use(csurf({ cookie: true }));

// Create CSRF exception filter
@Catch()
export class CsrfExceptionFilter implements ExceptionFilter {
  catch(exception: any, host: ArgumentsHost) {
    // Handle CSRF errors
  }
}
```

## Output

1. Installed `helmet` package
2. Created or updated security configuration using ConfigModule
3. Updated `src/main.ts` with Helmet middleware
4. Updated `src/main.ts` with CORS configuration
5. Configured CORS whitelist per environment
6. Helmet security headers active on all requests
7. CORS protection enabled with environment-based configuration
8. Optional: Created `.env.example` with security configuration
9. Production-ready security middleware configured

