# NestJS Application Architecture

## Overview

This document defines the architecture for our NestJS application. All code must strictly adhere to these guidelines. The architecture follows **Module-based Monolithic** pattern with **Domain-Driven Design** principles and **Clean Architecture** concepts.

## Architecture Pattern

**Module-based Monolithic Architecture**

- Single codebase with organized feature modules
- Each module is self-contained and isolated
- Shared infrastructure and utilities
- Easy to maintain and scale
- Potential for future microservices migration

## Core Principles

1. **SOLID Principles** - All code must follow SOLID principles
2. **Clean Architecture** - Separation of concerns and dependency inversion
3. **Domain-Driven Design** - Modules organized by business domains
4. **Repository Pattern** - Data access abstraction
5. **Dependency Injection** - NestJS built-in DI container
6. **Configuration Management** - Centralized configuration via ConfigModule

## Project Structure

- default nestjs project steup

## Module Structure

- default nestjs project setup

### Module Organization Rules

Every feature module MUST follow this structure:

```
src/modules/[feature-name]/
├── [feature-name].module.ts          # Module definition
├── [feature-name].controller.ts       # HTTP endpoints
├── [feature-name].service.ts          # Business logic
├── [feature-name].constants.ts        # Module constants
├── dto/                               # Data Transfer Objects
│   ├── create-[feature-name].dto.ts
│   ├── update-[feature-name].dto.ts
│   ├── [feature-name]-query.dto.ts
│   └── [feature-name]-response.dto.ts
├── entities/                          # TypeORM entities
│   └── [feature-name].entity.ts
├── repository/                        # Data access layer
│   ├── [feature-name].repository.ts
│   └── interfaces/
│       └── [feature-name]-repository.interface.ts
├── interfaces/                       # Module interfaces
│   └── [feature-name].interface.ts
└── [feature-name].spec.ts             # Unit tests
```

### Module File Responsibilities

#### 1. Module File (`[feature-name].module.ts`)

**Purpose:** Define module boundaries, imports, and exports

**Responsibilities:**

- Import required modules (TypeORM, Config, etc.)
- Register controllers
- Register providers (services, repositories)
- Export services for use in other modules

**Example:**

```typescript
@Module({
  imports: [
    TypeOrmModule.forFeature([FeatureEntity]),
    // Other module imports
  ],
  controllers: [FeatureController],
  providers: [FeatureService, FeatureRepository],
  exports: [FeatureService], // Export if used by other modules
})
export class FeatureModule {}
```

#### 2. Controller (`[feature-name].controller.ts`)

**Purpose:** Handle HTTP requests and responses

**Responsibilities:**

- Define routes and HTTP methods
- Request validation (via DTOs and ValidationPipe)
- Call service methods
- Return HTTP responses
- **MUST remain thin** - no business logic

**Rules:**

- ✅ Route definition
- ✅ Request validation
- ✅ Service method calls
- ✅ Response transformation
- ❌ Business logic
- ❌ Database queries
- ❌ Complex data manipulation

**Example:**

```typescript
@Controller('features')
@ApiTags('Features')
export class FeatureController {
  constructor(private readonly featureService: FeatureService) {}

  @Post()
  @UsePipes(ValidationPipe)
  async create(
    @Body() createDto: CreateFeatureDto
  ): Promise<FeatureResponseDto> {
    return this.featureService.create(createDto);
  }

  @Get()
  async findAll(
    @Query() queryDto: FeatureQueryDto
  ): Promise<PaginatedResponse<FeatureResponseDto>> {
    return this.featureService.findAll(queryDto);
  }
}
```

#### 3. Service (`[feature-name].service.ts`)

**Purpose:** Implement business logic

**Responsibilities:**

- Business logic implementation
- Orchestrate repository calls
- Data transformation
- Error handling
- **MUST NOT contain database queries** - delegate to repository

**Rules:**

- ✅ Business logic
- ✅ Data transformation
- ✅ Error handling
- ✅ Repository method calls
- ❌ Direct database queries
- ❌ TypeORM query builders
- ❌ Raw SQL

**Example:**

```typescript
@Injectable()
export class FeatureService {
  constructor(
    private readonly featureRepository: FeatureRepository,
    private readonly logger: Logger
  ) {}

  async create(createDto: CreateFeatureDto): Promise<FeatureEntity> {
    try {
      // Business logic validation
      if (createDto.amount < 0) {
        throw new BadRequestException('Amount cannot be negative');
      }

      // Delegate to repository
      return await this.featureRepository.create(createDto);
    } catch (error) {
      this.logger.error('Error creating feature', error.stack);
      throw error;
    }
  }
}
```

#### 4. Repository (`repository/[feature-name].repository.ts`)

**Purpose:** Handle all database operations

**Responsibilities:**

- All database queries
- TypeORM operations
- Query building
- Data persistence
- **ONLY layer that interacts with database**

**Rules:**

- ✅ All database queries
- ✅ TypeORM operations
- ✅ Query building
- ✅ Transaction management
- ❌ Business logic
- ❌ Data transformation (minimal)

**Example:**

```typescript
@Injectable()
export class FeatureRepository {
  constructor(
    @InjectRepository(FeatureEntity)
    private readonly repository: Repository<FeatureEntity>
  ) {}

  async create(createDto: CreateFeatureDto): Promise<FeatureEntity> {
    const entity = this.repository.create(createDto);
    return await this.repository.save(entity);
  }

  async findAll(query: FeatureQueryDto): Promise<[FeatureEntity[], number]> {
    const queryBuilder = this.repository.createQueryBuilder('feature');

    if (query.search) {
      queryBuilder.where('feature.name ILIKE :search', {
        search: `%${query.search}%`,
      });
    }

    queryBuilder
      .skip((query.page - 1) * query.limit)
      .take(query.limit)
      .orderBy('feature.createdAt', 'DESC');

    return await queryBuilder.getManyAndCount();
  }
}
```

#### 5. Entity (`entities/[feature-name].entity.ts`)

**Purpose:** Define database schema

**Responsibilities:**

- Database table definition
- Column definitions
- Relationships
- Indexes
- Constraints

**Rules:**

- ✅ TypeORM decorators
- ✅ Column definitions
- ✅ Relationships
- ✅ Indexes
- ✅ Extend BaseEntity when applicable

**Example:**

```typescript
@Entity('features')
export class FeatureEntity extends BaseEntity {
  @Column({ type: 'varchar', length: 255 })
  name: string;

  @Column({ type: 'text', nullable: true })
  description: string;

  @Column({ type: 'decimal', precision: 10, scale: 2 })
  amount: number;

  @Column({ type: 'enum', enum: FeatureStatus, default: FeatureStatus.DRAFT })
  status: FeatureStatus;

  @Index('idx_feature_status')
  @Index('idx_feature_created_at')
  // Additional indexes
}
```

#### 6. DTOs (`dto/`)

**Purpose:** Data validation and transformation

**Responsibilities:**

- Request validation
- Response transformation
- Data type safety

**Rules:**

- ✅ Use class-validator decorators
- ✅ Use class-transformer decorators
- ✅ Separate DTOs for create, update, query, response
- ✅ Validation on all input DTOs

**Example:**

```typescript
export class CreateFeatureDto {
  @IsString()
  @IsNotEmpty()
  @MinLength(3)
  @MaxLength(255)
  name: string;

  @IsString()
  @IsOptional()
  @MaxLength(5000)
  description?: string;

  @IsNumber()
  @IsNotEmpty()
  @Min(0)
  amount: number;
}

export class UpdateFeatureDto extends PartialType(CreateFeatureDto) {}

export class FeatureQueryDto {
  @IsOptional()
  @Type(() => Number)
  @Min(1)
  page?: number = 1;

  @IsOptional()
  @Type(() => Number)
  @Min(1)
  @Max(100)
  limit?: number = 10;

  @IsOptional()
  @IsString()
  search?: string;
}
```

## Naming Conventions

### Files and Folders

- **Files:** kebab-case (e.g., `tender.service.ts`, `create-tender.dto.ts`)
- **Folders:** kebab-case (e.g., `dto/`, `entities/`)

### Code

- **Classes:** PascalCase (e.g., `TenderService`, `CreateTenderDto`)
- **Variables/Functions:** camelCase (e.g., `tenderService`, `createTender`)
- **Constants:** UPPER_SNAKE_CASE (e.g., `MAX_LIMIT`, `DEFAULT_PAGE`)
- **Interfaces:** PascalCase with `I` prefix optional (e.g., `ITenderRepository` or `TenderRepository`)

### Modules

- Module name: PascalCase (e.g., `TenderModule`)
- Module folder: kebab-case (e.g., `tender/`)

## Configuration Management

### Rules

- **NEVER** access `process.env` directly
- **ALWAYS** use `ConfigService` from `@nestjs/config`
- **ALWAYS** validate environment variables using Joi schema
- **ALWAYS** define configuration in `src/config/`

### Configuration Files

#### `config/validation.schema.ts`

- validate schema using class-validator

#### `config/app.config.ts`

```typescript
import { registerAs } from '@nestjs/config';

export default registerAs('app', () => ({
  nodeEnv: process.env.NODE_ENV,
  port: parseInt(process.env.PORT, 10) || 3000,
  // ... other config
}));
```

#### Usage in Services

```typescript
@Injectable()
export class FeatureService {
  constructor(private readonly configService: ConfigService) {
    const port = this.configService.get<number>('app.port');
  }
}
```

## Database Access Pattern

### Repository Pattern

**Rule:** Services MUST use repositories for all database operations. Services MUST NOT contain query logic.

**Correct:**

```typescript
// Service
async findAll(query: QueryDto) {
  return this.repository.findAll(query);
}

// Repository
async findAll(query: QueryDto) {
  // All query logic here
  return this.repository.find({ where: {...} });
}
```

**Incorrect:**

```typescript
// Service - WRONG!
async findAll(query: QueryDto) {
  return this.repository.find({ where: {...} }); // Query logic in service
}
```

## Error Handling

### Exception Filters

- Use global exception filter
- Transform errors to consistent format
- Log errors appropriately

## Logging

### Rules

- **ALWAYS** use NestJS Logger (not `console.log`)
- Log at appropriate levels (error, warn, log, debug, verbose)
- Include context in log messages
- Never log sensitive information

### Example

```typescript
@Injectable()
export class FeatureService {
  private readonly logger = new Logger(FeatureService.name);

  async create(dto: CreateDto) {
    this.logger.log(`Creating feature: ${dto.name}`);
    // ...
  }
}
```

## Testing

### Test Structure

- Unit tests: `[feature-name].spec.ts` in module folder
- Integration tests: `tests/integration/`
- E2E tests: `tests/e2e/`

### Testing Rules

- Test all service methods
- Test all repository methods
- Test controller endpoints
- Mock dependencies
- Achieve >80% code coverage

## Module Isolation

### Rules

- Modules MUST be self-contained
- Modules communicate through exported services
- Avoid circular dependencies
- Use interfaces for module boundaries

### Module Communication

```typescript
// Module A exports service
@Module({
  exports: [ModuleAService],
})
export class ModuleA {}

// Module B imports and uses
@Module({
  imports: [ModuleA],
  providers: [ModuleBService],
})
export class ModuleB {
  // ModuleBService can inject ModuleAService
}
```

## Dependency Injection

### Rules

- Use constructor injection
- Use `@Injectable()` decorator
- Use `@InjectRepository()` for TypeORM repositories
- Use interfaces for abstraction

### Example

```typescript
@Injectable()
export class FeatureService {
  constructor(
    private readonly featureRepository: FeatureRepository,
    @Inject(IFeatureRepository)
    private readonly featureRepositoryInterface: IFeatureRepository,
    private readonly configService: ConfigService
  ) {}
}
```

## API Design

### RESTful Conventions

- Use proper HTTP methods (GET, POST, PATCH, DELETE)
- Use proper HTTP status codes
- Use resource-based URLs
- Version APIs: `/api/v1/`

### Response Format

```typescript
// Single resource
{
  "data": { ... }
}

// List with pagination
{
  "data": [...],
  "meta": {
    "total": 100,
    "page": 1,
    "limit": 10,
    "totalPages": 10
  }
}

// Error
{
  "statusCode": 400,
  "message": "Validation failed",
  "error": "Bad Request"
}
```

## Security

### Rules

- Validate all inputs
- Use authentication guards
- Use authorization guards
- Sanitize user input
- Use parameterized queries (TypeORM handles this)
- Never expose sensitive data

## Performance

### Rules

- Use database indexes appropriately
- Implement pagination for list endpoints
- Use transactions for multi-step operations
- Cache frequently accessed data
- Optimize database queries

## Migration Strategy

### Database Migrations

- Use TypeORM migrations
- Never use `synchronize: true` in production
- Create migrations for all schema changes
- Test migrations before deploying

## Code Quality

### Linting

- Follow ESLint rules
- Fix all linting errors
- Use Prettier for formatting

### Code Review Checklist

- [ ] Follows architecture guidelines
- [ ] Proper error handling
- [ ] Input validation
- [ ] Logging implemented
- [ ] Tests written
- [ ] Documentation updated

## Common Patterns

### Pagination

```typescript
interface PaginatedResponse<T> {
  data: T[];
  meta: {
    total: number;
    page: number;
    limit: number;
    totalPages: number;
  };
}
```

### Base Entity

```typescript
export abstract class BaseEntity {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;

  @DeleteDateColumn()
  deletedAt?: Date;
}
```

## Violations and Warnings

If code violates this architecture:

1. **WARN** the user immediately
2. **EXPLAIN** why it violates the architecture
3. **PROVIDE** an architecture-compliant solution
4. **DO NOT** implement the violating code

## Examples of Violations

❌ **Service with database queries:**

```typescript
// WRONG
async findAll() {
  return this.repository.find({ where: { status: 'active' } });
}
```

✅ **Correct:**

```typescript
// Service
async findAll() {
  return this.repository.findAll();
}

// Repository
async findAll() {
  return this.repository.find({ where: { status: 'active' } });
}
```

❌ **Controller with business logic:**

```typescript
// WRONG
@Post()
async create(@Body() dto: CreateDto) {
  if (dto.amount < 0) {
    throw new BadRequestException();
  }
  return this.service.create(dto);
}
```

✅ **Correct:**

```typescript
@Post()
async create(@Body() dto: CreateDto) {
  return this.service.create(dto);
}
```

❌ **Direct process.env access:**

```typescript
// WRONG
const port = process.env.PORT;
```

✅ **Correct:**

```typescript
const port = this.configService.get<number>('app.port');
```

## Summary

This architecture ensures:

- ✅ Maintainable and scalable codebase
- ✅ Clear separation of concerns
- ✅ Testable components
- ✅ Consistent patterns
- ✅ Production-ready code quality

**All code MUST comply with this architecture. No exceptions.**
