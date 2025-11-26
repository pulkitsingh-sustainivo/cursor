# Initialize Production-Level NestJS Project

When this file is referenced with 
<!-- `@.cursor/commands/init.md`, -->
create a NestJS bolerplate using the cli command npm i -g @nestjs/cli and then nest new project-name.

## Project Structure

- default nestjs folder structure using the command npm i -g @nestjs/cli

## Module Template Structure

When creating a new module, use this structure:

```
src/modules/[module-name]/
├── [module-name].module.ts
├── [module-name].controller.ts
├── [module-name].service.ts
├── dto/
│   ├── create-[module-name].dto.ts
│   ├── update-[module-name].dto.ts
│   └── [module-name]-query.dto.ts
├── entities/
│   └── [module-name].entity.ts
└── [module-name].spec.ts
```

## Implementation Instructions

When this file is referenced, follow these steps:

1. **Create folder structure** - Create all directories as specified
2. **Create configuration files** - package.json, tsconfig.json, etc.
3. **Create source files** - All TypeScript files in src/
4. **Create environment files** - .env.example, .env.development
5. **Create documentation** - README.md, docs/architecture.md
6. **Create Docker setup** - docker-compose.yml
7. **Initialize git** - Create .gitignore
8. **Install dependencies** - Run npm install

## Code Standards

- Use TypeScript with strict typing
- Follow NestJS best practices
- Use dependency injection
- Implement proper error handling
- Add validation to all DTOs
- Use NestJS Logger instead of console.log
- Follow the module-based architecture
- Write unit tests for services
- Write integration tests for controllers

## Next Steps After Initialization

1. Configure database connection
2. Create your first module
3. Run migrations
4. Start development server
5. Test endpoints
