# NestJS + Cursor Productivity Documentation

## Overview
      This guide provides a comprehensive, production-grade blueprint for building scalable NestJS + React applications using Cursor. It outlines best practices for architecture, configuration, testing, automation, and tooling.

      The objective is to help developers and teams produce clean, maintainable, enterprise-ready applications using Cursor’s AI-assisted workflows.
      This document covers everything from modular backend architecture, TypeORM patterns, DTO rules, config module design, and queue systems, to testing suites, deployment concepts, and automation rules. Whether you're building a monolith or microservices, this serves as a consistent reference for structuring your application in production.


## Overview

- Faster development
- Consistent codebase
- Predictable folder structure
- Easier onboarding
- Cleaner APIs
- More secure apps
- Infra + Dockerization makes deployment repeatable

### Goal:
“NestJS project should be written using our standards — DTOs, entities, guards, filters, interceptors, Docker, Compose, and infrastructure.”

### Folder Structures

#### Modular
```
src/
 ├── main.ts
 ├── app.module.ts
 ├── config/                         
 │    ├── app.config.ts
 │    ├── db.config.ts
 │    ├── redis.config.ts
 │    └── cache.config.ts
 ├── common/
 │    ├── database/
 │    │     ├── entities/             
 │    │     │     ├── base.entity.ts
 │    │     │     └── abstract.entity.ts
 │    │     └── ormconfig.ts
 │    ├── cache/                      
 │    │     ├── redis.module.ts
 │    │     ├── redis.service.ts
 │    │     └── index.ts
 │    ├── queue/                      
 │    │     ├── queue.module.ts
 │    │     ├── queue.service.ts
 │    │     └── processors/
 │    ├── external-services/          
 │    │     ├── email/
 │    │     ├── sms/
 │    │     ├── storage/
 │    │     └── payment/
 │    ├── decorators/
 │    ├── dto/
 │    ├── enums/
 │    ├── exceptions/
 │    ├── filters/
 │    ├── guards/
 │    ├── interceptors/
 │    ├── middleware/
 │    ├── pipes/
 │    └── utils/
 ├── auth/                            
 │    ├── auth.module.ts
 │    ├── controllers/
 │    ├── services/
 │    ├── dto/
 │    ├── entities/
 │    ├── repositories/
 │    ├── strategies/
 │    └── guards/
 ├── module/                        
 │    ├── module.module.ts
 │    ├── controllers/
 │    ├── services/
 │    ├── dto/
 │    ├── entities/
 │    └── repositories/
 └── tests/                           
      ├── unit/
      ├── integration/
      └── e2e/
```

### Microservices
```
services/                           # each folder is an independently deployable service
 ├── api-gateway/                   # API Gateway (NestJS + Fastify/Express)
 │    ├── src/
 │    │    ├── main.ts
 │    │    ├── app.module.ts
 │    │    ├── controllers/
 │    │    ├── plugins/             # auth, rate-limit, cors, helmet
 │    │    ├── interceptors/
 │    │    ├── filters/
 │    │    └── grpc-proxies/        # optional: gRPC clients or adapters
 │    └── Dockerfile
 ├── auth-service/                  # Auth & Identity (JWT/OAuth, sessions)
 │    ├── src/
 │    │    ├── main.ts
 │    │    ├── app.module.ts
 │    │    ├── controllers/
 │    │    ├── services/
 │    │    ├── dto/
 │    │    ├── entities/
 │    │    ├── repositories/
 │    │    ├── strategies/
 │    │    └── events/              # publishes auth events
 │    └── Dockerfile
 ├── feature1-service/                 # User management (profile, preferences)
 │    ├── src/
 │    │    ├── main.ts
 │    │    ├── app.module.ts
 │    │    ├── controllers/
 │    │    ├── services/
 │    │    ├── dto/
 │    │    ├── entities/
 │    │    ├── repositories/
 │    │    └── consumers/            # message handlers (queue/kafka)
 │    └── Dockerfile
 ├── feature2-service/              # Domain service example
 │    ├── src/
 │    │    ├── main.ts
 │    │    ├── app.module.ts
 │    │    ├── controllers/
 │    │    ├── services/
 │    │    ├── dto/
 │    │    ├── entities/
 │    │    ├── repositories/
 │    │    └── consumers/
 │    └── Dockerfile
 ├── notification-service/          # Email/SMS/push (external integrations)
 │    ├── src/
 │    │    ├── main.ts
 │    │    ├── app.module.ts
 │    │    ├── services/
 │    │    ├── providers/
 │    │    └── consumers/
 │    └── Dockerfile
 └── shared-libs/                   # internal package(s) used by multiple services
      ├── config/                    # typed config factories (+ zod/joi)
      ├── contracts/                 # DTOs, interfaces, protobufs / openapi fragments
      ├── utils/
      ├── logging/                   # structured logging setup
      ├── metrics/                   # metrics helpers
      └── README.md
```

## Command Based Features Integration (.cursor/commands/nestjs-config.md)
- Modular/Microservices based Architecture
- Config Module (`@nestjs/config`) [Secrets] [Database (TypeOrm/PrismaOrm/MongooseOrm)] [Redis] [3rd-party services] [Feature flag] No direct process.env usage in the codebase.
- Validation Pipeline [class-validators] [class-transformers]
- Exception Filters [create production ready exception filter] [@catch]
- Logging (`@nestjs/common`) [Setup production level nestjs logging]
- Authentication (JWT/OAuth2)
- Authorization (RBAC)
- Interceptors [CacheInterceptor with redis cache] [Ensures Consistent API Response Formatting] 
- Guards [authentication] [authorization]
- Rate Limiting ['@nestjs/throttler] [global & local]
- ORM Setup  
- Migrations  
- API Versioning  
- Swagger  
- Redis Cache  
- Queues (BullMQ)  
- Testing (Unit/Integration/E2E)  
- CI/CD  
- Security (Helmet, CORS, CSRF)  
- Health Checks & Metrics  

---

## Rules

### Cursor Prompt for Entities
Rules:
* Use **UUID primary keys**
* Use **snake_case** for all database columns
* Every entity must **extend `BaseEntity`** (timestamps required, soft-delete optional)
* One migration per change — **no auto-sync** in production
* Use the **Repository pattern**; never use `getConnection().query()`
* Relations must be **strict**, with `eager: false`
* Entities should **never contain business logic**

### Cursor Prompt Rules for DTOs
Rules:
* Must use strict, explicit validation
* Always create:

  * `CreateXDto`
  * `UpdateXDto` (using `PartialType`)
* Add `@ApiProperty()` for Swagger on all fields
* No optional values in `CreateDto` unless absolutely required
* Use `readonly` properties
* Use transformers for types (e.g., `@Type(() => Number)`)

### class-validator & class-transformer Rules
Rules:
* Must use only the following validation decorators:
  * `@IsString()`
  * `@IsNumber()`
  * `@IsUUID()`
  * `@IsBoolean()`
  * `@IsEmail()`
  * `@IsEnum()`
  * `@IsOptional()`
* Must use `@Type(() => ...)` for all required transformations
* Avoid any other validation decorators in DTOs
* No custom validation unless absolutely required
* Always validate incoming requests using `ValidationPipe`

### Cursor Prompt Rules – Config Module
Rules:
* Every config must have schema validation (Zod or Joi)
* All config files must be placed in `/config/*.config.ts`
* `ConfigService` must always be injected — no static access
* Never use raw environment variables directly in code

### Cursor Prompt Rules – Logging
Rules:
* Never use `console.log`
* Must use NestJS `Logger` or `pino`
* Logs must include:
  * timestamp
  * context
  * request-id
  * level

### Cursor Prompt Rules – Exception Filters
Rules:
* One global exception filter must handle:
  * `HttpException`
  * `ValidationException`
  * Database errors
  * Uncaught exceptions
* Always return a **consistent error shape**

### Cursor Prompt Rules – Interceptors
Rules:
* `ResponseInterceptor` → always format responses
* `CacheInterceptor` → must use Redis for GET routes
* `TimeoutInterceptor` → prevent hanging services

### Cursor Prompt Rules – Guards
Rules:
* `AuthGuard` → validates JWT
* `RolesGuard` → RBAC
* `ConditionsGuard` → ABAC (optional)
* Guards must be composable

### Cursor Prompt Rules – Rate Limiting
Rules:
* Global throttler for entire application
* Local throttlers for sensitive routes
* Dynamic limits must be set based on user roles

### Cursor Prompt Rules – Security
Rules:
* Use **Helmet**
* CORS whitelist per environment
* Enable CSRF for web form submissions
* Disable stack traces in production

### Cursor Prompt Rules – Queues (Bull / BullMQ)
Rules:
* Every queue must define:
  * processor
  * event handlers
  * retry strategy
* Never run heavy jobs inside controllers/services
* Use queues for:
  * email
  * SMS
  * exports
  * async workflows

### Cursor Prompt Rules – Redis Cache
Rules:
* All caching must use `CacheModule`
* TTL must be defined per key
* No infinite TTL values allowed
* Use namespaced keys: `app:module:key`

### Cursor Prompt Rules – API Versioning
Rules:
* Always enable versioning at bootstrap
* Use URI or Header versioning
* Version breaking changes

### Cursor Prompt Rules – Swagger
Rules:
* Use `DocumentBuilder`
* Use tags for modules
* Use DTOs only
* Hide internal / private APIs

### Cursor Prompt Rules – Testing
Types of testing required:
* Unit Testing
* Integration Testing
* E2E Testing
* Functional / API Testing
* BDD Testing
* Contract Testing (microservices, events, messaging)
* Performance Testing
* Security Testing

## NestJS – Types of Testing
1. Unit Testing  
2. Integration Testing  
3. E2E Testing  
4. Functional/API Testing  
5. BDD Testing  
6. Contract Testing  
7. Performance Testing  
8. Security Testing  

## Dockerizing a NestJS Application

Rules:
* Use **multi-stage Docker builds** (builder → production image)
* Base image must be **node:18-alpine** or higher
* The builder stage must:
  * install dependencies (`npm ci`)
  * build the project (`npm run build`)
* The runtime stage must:
  * copy only `dist/` and required production deps
  * run the server as a non-root user
* Do not copy dev dependencies into production image
* Expose required port (`3000` default)
* Use `CMD ["node", "dist/main.js"]`
* Use `.dockerignore` to exclude unnecessary files
* Healthcheck must be included (curl → `/health`)
* No hardcoded environment variables inside the Dockerfile
* Build must be reproducible and deterministic

## Cursor Prompt – Docker Compose for NestJS
Rules:
* Compose file must use version `"3.9"`
* Must include services like:

  * `app` (NestJS)
  * `db` (Postgres/MySQL)
  * `redis` (for cache/queues)
  * `worker` (BullMQ worker)
* Each service must have:

  * separate container
  * restart policy (`always` or `on-failure`)
  * healthchecks
  * environment variables via `.env`
* Never embed secrets directly in `docker-compose.yml`
* Use named volumes for:

  * database data
  * redis data (optional)
* Use a shared network for communication
* Expose only required ports
* `app` must depend on healthy `db` and `redis`
* `app` should run migrations automatically on startup (optional rule)
* Build context must always point to project root
* Logs must be in JSON format if possible
* Use `profiles:` to separate worker jobs, migrations, admin tools
* No Windows-specific paths — must run on Linux server

## .cursorignore 
 
* Add ignore files for cursor

## .cursorrules

```
{
    "rules": [
        {
            "pattern": "**/*.ts",
            "instructions": [
                "Before writing any code, ALWAYS read /docs/architecture.md.",
                "Follow the NestJS production modular monolithic architecture strictly.",
                "Ensure the code aligns with required folder structure, naming conventions, and layering rules.",
                "Controllers must handle routing only.",
                "Services must contain business logic only and must not contain DB queries.",
                "Repositories must contain all database operations.",
                "All environment usage must go through ConfigModule, never process.env.",
                "DTOs must always use class-validator + class-transformer.",
                "Create files only in the appropriate module folder as defined in architecture.md.",
                "If the user asks for something that violates the architecture, STOP and warn them."
            ]
        },
        {
            "pattern": "src/modules/**",
            "instructions": [
                "Each module must contain: controller, service, module, dto/, repository/, entities/, interfaces/, constants file.",
                "Use PascalCase for classes, kebab-case for files, camelCase for variables.",
                "When generating new modules, auto-create the folder structure: dto/, repository/, entities/, interfaces/."
            ]
        },
        {
            "pattern": "src/config/**",
            "instructions": [
                "Ensure environment variable validation is consistent with validation.schema.ts.",
                "Expose configuration only via ConfigService.",
                "Never hardcode environment values inside the code."
            ]
        },
        {
            "pattern": "src/jobs/**",
            "instructions": [
                "Queue definitions must be in queues/, processors in processors/.",
                "Services enqueue jobs; processors handle job logic."
            ]
        },
        {
            "pattern": "src/integrations/**",
            "instructions": [
                "External service integrations must be isolated.",
                "Never mix external service code inside modules.",
                "Use provider-based integration patterns."
            ]
        }
    ],
    "prompts": [
        {
            "name": "architecture_guard",
            "prompt": "Before writing any code, you MUST check the contents of /docs/architecture.md. If the request violates the architecture, warn the user and rewrite the solution in a compliant way."
        },
        {
            "name": "create_module",
            "prompt": "When asked to create a new NestJS module, automatically generate the folder structure: src/modules/<name>/{dto,entities,repository,interfaces}/ and include <name>.module.ts, <name>.service.ts, <name>.controller.ts, <name>.constants.ts. Use production ready NestJS style."
        }
    ]
}

```
