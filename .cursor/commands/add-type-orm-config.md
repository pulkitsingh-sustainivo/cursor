# Add TypeORM Configuration

## Input

Optional environment variables that may be provided:
- `DB_TYPE` - Database type (postgres, mysql, mariadb, sqlite, etc.) - defaults to 'postgres'
- `DB_HOST` - Database host - defaults to 'localhost'
- `DB_PORT` - Database port - defaults to 5432 (postgres) or 3306 (mysql)
- `DB_USERNAME` - Database username
- `DB_PASSWORD` - Database password
- `DB_NAME` - Database name

## Rules

- TypeORM configuration must use ConfigModule (never use process.env directly)
- Create config files in: `src/config/`
- Use kebab-case for files.
- Use PascalCase for classes.
- All database configuration must go through ConfigService
- Environment variables must have schema validation
- Use Repository pattern - never use `getConnection().query()`
- Entities must extend BaseEntity (with timestamps)

## Folder Structure to Generate

src/config/
├── database.config.ts
└── validation.schema.ts (if not exists)

src/common/
└── database/
    ├── base.entity.ts
    └── entities/ (directory for shared entities)

## Tasks to Perform

1. **Install required dependencies**
   - Install TypeORM: `npm install @nestjs/typeorm typeorm`
   - Install database driver (default postgres): `npm install pg`
   - Install ConfigModule: `npm install @nestjs/config`
   - Install validation library: `npm install joi` or `npm install zod` (for schema validation)

2. **Create ConfigModule setup**
   - Create `src/config/database.config.ts`:
     - Export function that returns TypeORM configuration using ConfigService
     - Use ConfigService.get() for all database settings
     - Include all TypeORM options (type, host, port, username, password, database, entities, synchronize, logging, etc.)
   - Create or update `src/config/validation.schema.ts`:
     - Define Joi/Zod schema for database environment variables
     - Validate DB_TYPE, DB_HOST, DB_PORT, DB_USERNAME, DB_PASSWORD, DB_NAME

3. **Create BaseEntity**
   - Create `src/common/database/base.entity.ts`:
     - Abstract class that extends TypeORM's BaseEntity
     - Include timestamps: `@CreateDateColumn()` and `@UpdateDateColumn()`
     - Include UUID primary key: `@PrimaryGeneratedColumn('uuid')`
     - Include optional soft delete: `@DeleteDateColumn()` (optional)

4. **Update app.module.ts**
   - Import `ConfigModule` from `@nestjs/config`
   - Import `TypeOrmModule` from `@nestjs/typeorm`
   - Configure ConfigModule:
     ```typescript
     ConfigModule.forRoot({
       isGlobal: true,
       validationSchema: validationSchema, // from validation.schema.ts
       envFilePath: ['.env'],
     })
     ```
   - Configure TypeOrmModule:
     ```typescript
     TypeOrmModule.forRootAsync({
       imports: [ConfigModule],
       useFactory: (configService: ConfigService) => databaseConfig(configService),
       inject: [ConfigService],
     })
     ```

5. **Create .env.example file** (if not exists)
   - Add database configuration variables:
     ```
     DB_TYPE=postgres
     DB_HOST=localhost
     DB_PORT=5432
     DB_USERNAME=your_username
     DB_PASSWORD=your_password
     DB_NAME=your_database_name
     ```

6. **Update entities** (update existing cat entity)
   - Update `src/cat/entities/cat.entity.ts`:
     - Import and extend `BaseEntity` from `src/common/database/base.entity`
     - Add TypeORM decorators: `@Entity('cats')`
     - Use snake_case for column names: `@Column({ name: 'column_name' })`
     - All columns must use snake_case naming

7. **Update repositories** (update existing cat repository)
   - Update `src/cat/repositories/cat.repositories.ts`:
     - Inject TypeORM repository using `@InjectRepository(CatEntity)`
     - Use Repository pattern with TypeORM's Repository methods
     - Implement all CRUD methods using TypeORM repository

8. **Update cat.module.ts**
   - Import `TypeOrmModule` and add `TypeOrmModule.forFeature([CatEntity])` to imports

## Database Configuration Pattern

```typescript
// src/config/database.config.ts
export const databaseConfig = (configService: ConfigService) => ({
  type: configService.get('DB_TYPE'),
  host: configService.get('DB_HOST'),
  port: configService.get('DB_PORT'),
  username: configService.get('DB_USERNAME'),
  password: configService.get('DB_PASSWORD'),
  database: configService.get('DB_NAME'),
  entities: [__dirname + '/../**/*.entity{.ts,.js}'],
  synchronize: configService.get('NODE_ENV') !== 'production', // false in production
  logging: configService.get('NODE_ENV') === 'development',
  // Add SSL config for production if needed
});
```

## BaseEntity Pattern

```typescript
// src/common/database/base.entity.ts
import { PrimaryGeneratedColumn, CreateDateColumn, UpdateDateColumn, DeleteDateColumn, BaseEntity as TypeOrmBaseEntity } from 'typeorm';

export abstract class BaseEntity extends TypeOrmBaseEntity {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @CreateDateColumn({ name: 'created_at' })
  createdAt: Date;

  @UpdateDateColumn({ name: 'updated_at' })
  updatedAt: Date;

  @DeleteDateColumn({ name: 'deleted_at', nullable: true })
  deletedAt?: Date;
}
```

## Entity Rules

- Use UUID primary keys
- Use snake_case for all database columns
- Every entity must extend `BaseEntity`
- Use `@Entity('table_name')` decorator with explicit table name
- Relations must be strict with `eager: false`
- Entities should never contain business logic

## Output

1. Installed TypeORM and required dependencies (`@nestjs/typeorm`, `typeorm`, `pg`, `@nestjs/config`, `joi` or `zod`)
2. Created `src/config/database.config.ts` with TypeORM configuration
3. Created or updated `src/config/validation.schema.ts` with database environment variable validation
4. Created `src/common/database/base.entity.ts` with BaseEntity class
5. Updated `src/app.module.ts` with ConfigModule and TypeOrmModule configuration
6. Created `.env.example` file with database configuration template
7. Updated `src/cat/entities/cat.entity.ts` to extend BaseEntity and use TypeORM decorators
8. Updated `src/cat/repositories/cat.repositories.ts` to use TypeORM Repository pattern
9. Updated `src/cat/cat.module.ts` to include TypeOrmModule.forFeature([CatEntity])
10. TypeORM configured and ready to use with ConfigService (no direct process.env usage)

