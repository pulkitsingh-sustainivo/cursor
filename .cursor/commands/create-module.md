# create nestjs modules

Create module wise nestjs features
every features will be treated as modules
creating nestjs module folder and naming

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

### Files and Folders

- **Files:** kebab-case (e.g., `tender.service.ts`, `create-tender.dto.ts`)
- **Folders:** kebab-case (e.g., `dto/`, `entities/`)

### Log changes 

- create CHANGELOG.md and preview the changes.