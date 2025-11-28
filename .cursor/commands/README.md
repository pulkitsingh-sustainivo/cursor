# Cursor Commands Reference

## How to Use Command Files

Reference any command file using `@.cursor/commands/[filename]` in your Cursor chat to trigger the instructions in that file.

## Available Commands

### Project Setup Commands

#### init.md

**Usage:** `@.cursor/commands/init.md <project-name>`

Initializes a clean, production-ready NestJS project using Nest CLI.

**Features:**

- Creates new NestJS application using `nest new`
- Installs dependencies
- Starts the development server

**Example:**

```
@.cursor/commands/init.md my-nest-app
```

#### create-module.md

**Usage:** `@.cursor/commands/create-module.md <module-name>`

Creates a new NestJS module with complete folder structure.

**Features:**

- Creates module, controller, services, DTOs, entities, and repositories
- Follows production-ready architecture patterns
- Uses kebab-case for files, PascalCase for classes

**Example:**

```
@.cursor/commands/create-module.md user
```

### Configuration Commands

#### add-validation-pipeline.config.md

**Usage:** `@.cursor/commands/add-validation-pipeline.config.md`

Adds global validation pipeline using class-validator and class-transformer.

**Features:**

- Global ValidationPipe configuration
- Automatic DTO transformation
- Request validation
- Whitelist and forbid non-whitelisted properties

#### add-exception-filter.config.md

**Usage:** `@.cursor/commands/add-exception-filter.config.md`

Adds global exception filter to handle all errors consistently.

**Features:**

- Handles HttpException, ValidationException, Database errors
- Consistent error response format
- Environment-aware (hides stack traces in production)
- Uses NestJS Logger for error logging

#### add-swagger-documentation.config.md

**Usage:** `@.cursor/commands/add-swagger-documentation.config.md`

Adds Swagger/OpenAPI documentation to the application.

**Features:**

- Automatic API documentation generation
- Swagger UI at `/api`
- DTO and controller documentation
- JWT Bearer auth support

#### add-type-orm-config.md

**Usage:** `@.cursor/commands/add-type-orm-config.md`

Configures TypeORM with database connection and base entity.

**Features:**

- TypeORM module setup with ConfigModule
- BaseEntity with timestamps and UUID
- Database configuration through ConfigService
- Environment variable validation

#### add-jwt-authentication.md

**Usage:** `@.cursor/commands/add-jwt-authentication.md`

Adds JWT authentication to the application.

**Features:**

- Complete auth module with login/register
- JWT strategy with Passport
- Auth guards and roles guards
- Password hashing with bcrypt
- Current user decorator

#### add-logger.config.md

**Usage:** `@.cursor/commands/add-logger.config.md`

Configures production-level logging using NestJS Logger.

**Features:**

- Replaces console.log with NestJS Logger
- Request/response logging interceptor
- Request ID middleware for tracing
- Structured logging with context

#### add-rate-limitter.config.md

**Usage:** `@.cursor/commands/add-rate-limitter.config.md`

Adds rate limiting to the application using @nestjs/throttler.

**Features:**

- Global rate limiting
- Per-route rate limits
- Role-based rate limits (optional)
- ConfigModule integration

#### add-security.config.md

**Usage:** `@.cursor/commands/add-security.config.md`

Adds security middleware (Helmet & CORS) to the application.

**Features:**

- Helmet for HTTP security headers
- CORS configuration per environment
- Content Security Policy
- Production-ready security settings

### Utility Commands

#### edit.md

**Usage:** `@.cursor/commands/edit.md`

Guidelines for editing code with Cursor.

#### fix.md

**Usage:** `@.cursor/commands/fix.md`

Guidelines for fixing errors with Cursor.

#### doc.md

**Usage:** `@.cursor/commands/doc.md`

Guidelines for generating documentation.

#### chat.md

**Usage:** `@.cursor/commands/chat.md`

Guidelines for using chat functionality.

#### create-schema.md

**Usage:** `@.cursor/commands/create-schema.md`

Creates database schema definitions.

#### create-pr.md

**Usage:** `@.cursor/commands/create-pr.md`

Creates pull request templates.

#### dockerize.md

**Usage:** `@.cursor/commands/dockerize.md`

Dockerizes the NestJS application.

## Quick Start Guide

### 1. Initialize a New NestJS Project

```
@.cursor/commands/init.md my-nest-app
```

### 2. Create Your First Module

```
@.cursor/commands/create-module.md user
```

### 3. Add Essential Configurations

Set up the core features for your application:

```
@.cursor/commands/add-validation-pipeline.config.md
@.cursor/commands/add-exception-filter.config.md
@.cursor/commands/add-swagger-documentation.config.md
@.cursor/commands/add-logger.config.md
@.cursor/commands/add-security.config.md
```

### 4. Add Database and Authentication (Optional)

```
@.cursor/commands/add-type-orm-config.md
@.cursor/commands/add-jwt-authentication.md
```

### 5. Add Rate Limiting (Optional)

```
@.cursor/commands/add-rate-limitter.config.md
```

## Command Execution Order

For a production-ready NestJS application, execute commands in this recommended order:

1. **Project Setup**

   - `init.md` - Initialize project
   - `create-module.md` - Create your modules

2. **Core Configuration**

   - `add-validation-pipeline.config.md` - Request validation
   - `add-exception-filter.config.md` - Error handling
   - `add-logger.config.md` - Logging

3. **API Features**

   - `add-swagger-documentation.config.md` - API documentation
   - `add-security.config.md` - Security headers

4. **Database & Auth** (if needed)

   - `add-type-orm-config.md` - Database setup
   - `add-jwt-authentication.md` - Authentication

5. **Additional Features** (if needed)
   - `add-rate-limitter.config.md` - Rate limiting

## Notes

- All configuration commands follow the architecture rules defined in `/docs/architecture.md`
- Commands use ConfigModule for environment variables (never direct `process.env`)
- All commands follow naming conventions: kebab-case for files, PascalCase for classes
- Commands are designed to be idempotent where possible
