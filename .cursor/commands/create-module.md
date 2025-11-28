# Create New NestJS Module

## Input

<module-name> = the feature/module name in lowercase with dashes (e.g. user, order-history, product-review)

## Rules

- Create the module inside: src/<module-name>/
- Use kebab-case for files.
- Use PascalCase for classes.
- Use camelCase for variables.
- Generate all folder and file structures exactly as defined below.
- Module name must dynamically replace `<module-name>` everywhere.

## Folder Structure to Generate

src/<module-name>/
├── <module-name>.module.ts
├── controllers/
│ └── <module-name>.controller.ts
├── services/
│ ├── create-<module-name>.ts
│ ├── get-<module-name>.ts
│ ├── getAll-<module-name>.ts
│ ├── update-<module-name>.ts
│ └── delete-<module-name>.ts
├── dto/
│ ├── create-<module-name>.dto.ts
│ ├── get-<module-name>.dto.ts
│ ├── getAll-<module-name>.dto.ts
│ ├── update-<module-name>.dto.ts
│ └── delete-<module-name>.dto.ts
├── entities/
│ └── <module-name>.entity.ts
└── repositories/
└── <module-name>.repositories.ts

## Tasks to Perform

1. Create the folder structure.
2. Generate empty boilerplate code in each file.
3. Create the module file with imports + provider configuration.
4. Ensure service files export a class named like:
   - Create<PascalModuleName>Service
   - Get<PascalModuleName>Service
   - GetAll<PascalModuleName>Service
   - Update<PascalModuleName>Service
   - Delete<PascalModuleName>Service
5. For DTOs:
   - Create<PascalModuleName>Dto
   - Get<PascalModuleName>Dto
   - GetAll<PascalModuleName>Dto
   - Update<PascalModuleName>Dto
   - Delete<PascalModuleName>Dto
6. For Entity:
   - <PascalModuleName>Entity
7. For Repository:
   - <PascalModuleName>Repository

## Output

Generate full folder + file structure + boilerplate content for the module: <module-name>.
