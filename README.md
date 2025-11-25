# Project Documentation

This document explains the purpose, usage, and structure of key configuration files and folders in this NestJS project.

---

## 📁 `.cursor` Folder

### What is it?

The `.cursor` folder contains Cursor IDE-specific configuration files that help customize how the AI assistant behaves when working with your codebase.

### Structure

```
.cursor/
├── commands/          # Custom command definitions
│   ├── chat.md       # Chat command handler
│   ├── doc.md        # Documentation command handler
│   ├── edit.md       # Edit command handler
│   ├── fix.md        # Fix command handler
│   ├── init.md       # Project initialization command
│   └── README.md     # Commands reference guide
├── rules.md          # General coding rules and preferences
└── doc.md            # Documentation standards and rules
```

### When to Use

**Use the `.cursor` folder when:**

- You want to customize how Cursor AI behaves in your project
- You need to define custom commands for common tasks
- You want to set project-specific coding rules and preferences
- You need to standardize documentation practices

### How to Use

#### 1. **Commands Folder** (`commands/`)

Reference command files using `@.cursor/commands/[filename]` syntax:

**Example:**

```
@.cursor/commands/chat.md Create a user authentication module
```

**Available Commands:**

- **`chat.md`** - Use `/chat 'prompt'` to implement features with AI assistance
- **`doc.md`** - Use `/doc 'prompt'` to generate or update documentation
- **`edit.md`** - Use `/edit 'prompt'` to modify existing code
- **`fix.md`** - Use `/fix 'prompt'` to identify and fix errors
- **`init.md`** - Use to initialize a new NestJS project structure

#### 2. **Rules File** (`rules.md`)

Contains general coding rules that Cursor AI follows:

- Safe editing policies (don't break existing code)
- Code style preferences (TypeScript, type annotations)
- Styling guidelines (Tailwind CSS)
- Code quality standards (linting, formatting)

**When to modify:** Update this file when you want to change project-wide coding standards or preferences.

#### 3. **Documentation Rules** (`doc.md`)

Defines documentation standards for the project:

- JSDoc format requirements
- README file standards
- API documentation guidelines
- Inline comment rules

**When to modify:** Update when you want to change documentation style or requirements.

---

## 📄 `.cursorignore` File

### What is it?

The `.cursorignore` file tells Cursor IDE which files and folders to exclude from AI context and indexing. Similar to `.gitignore`, but specifically for Cursor's AI features.

### Current Contents

```
node_modules
dist
coverage
build
.git
.env
.env.*
logs
tmp
cache
.cursor
.cursorignore
.cursorrules
```

### When to Use

**Add files/folders to `.cursorignore` when:**

- Files are too large and slow down AI processing
- Files contain sensitive information (API keys, secrets)
- Files are generated automatically (build outputs, node_modules)
- Files are not relevant to code understanding (logs, cache)
- You want to exclude configuration files from AI context

### How to Use

**Add entries using patterns:**

```bash
# Ignore specific file
filename.txt

# Ignore all files with extension
*.log

# Ignore folder and all contents
folder-name/

# Ignore files matching pattern
*.env.*
```

**Example additions:**

```bash
# Large data files
*.csv
*.json.bak

# Generated files
*.generated.ts

# Test coverage
coverage/
```

**Note:** Files in `.cursorignore` won't be read by Cursor AI, which can improve performance but may limit context understanding.

---

## 📄 `.cursorrules` File

### What is it?

The `.cursorrules` file is a JSON configuration that defines architecture rules and patterns that Cursor AI must follow when generating or modifying code. It enforces project-specific standards.

### Structure

The file contains:

- **Rules** - Pattern-based instructions for specific file types
- **Prompts** - Named prompt templates for common tasks

### When to Use

**Modify `.cursorrules` when:**

- You want to enforce architectural patterns
- You need to add rules for new file types or patterns
- You want to create reusable prompt templates
- You need to change coding standards for specific directories

### How to Use

#### Understanding the Rules Structure

**Pattern-based Rules:**

```json
{
  "pattern": "**/*.ts",
  "instructions": ["Rule 1", "Rule 2"]
}
```

**Current Rules:**

1. **TypeScript files (`**/\*.ts`)\*\* - Enforces NestJS architecture, layering rules, and coding standards
2. **Modules (`src/modules/**`)\*\* - Enforces module structure and naming conventions
3. **Config (`src/config/**`)\*\* - Enforces configuration management rules
4. **Jobs (`src/jobs/**`)\*\* - Enforces queue and processor patterns
5. **Integrations (`src/integrations/**`)\*\* - Enforces external service isolation

#### Adding New Rules

**Example: Adding a rule for test files:**

```json
{
  "pattern": "**/*.spec.ts",
  "instructions": [
    "All test files must use Jest framework",
    "Mock all external dependencies",
    "Achieve minimum 80% code coverage"
  ]
}
```

#### Using Prompts

**Available Prompts:**

- `architecture_guard` - Checks architecture compliance before coding
- `create_module` - Generates new NestJS modules with proper structure

**How prompts work:** When you reference these prompts, Cursor AI will apply the defined behavior automatically.

### Key Rules Enforced

1. **Architecture Compliance**

   - Must read `/docs/architecture.md` before coding
   - Follow NestJS modular monolithic architecture
   - Controllers handle routing only
   - Services contain business logic only (no DB queries)
   - Repositories contain all database operations

2. **Configuration Management**

   - Never use `process.env` directly
   - Always use `ConfigModule` and `ConfigService`
   - Validate environment variables

3. **Module Structure**

   - Each module must have: controller, service, module, dto/, repository/, entities/, interfaces/
   - Use PascalCase for classes, kebab-case for files

4. **Code Quality**
   - DTOs must use class-validator + class-transformer
   - Files must be created in appropriate module folders

**Important:** If code violates these rules, Cursor AI will warn you and suggest compliant alternatives.

---

## 📁 `docs` Folder

### What is it?

The `docs` folder contains project documentation, architecture guides, and technical specifications.

### Current Contents

```
docs/
└── architecture.md    # Complete NestJS application architecture guide
```

### When to Use

**Use the `docs` folder when:**

- You need to document architectural decisions
- You want to create technical guides for developers
- You need to maintain API documentation
- You want to document setup and deployment procedures
- You need to create migration guides or changelogs

### How to Use

#### 1. **Architecture Documentation** (`architecture.md`)

**Purpose:** Defines the complete architecture, patterns, and conventions for the NestJS application.

**Contains:**

- Architecture pattern (Module-based Monolithic)
- Project structure and folder organization
- Module structure requirements
- File responsibilities (Controller, Service, Repository, Entity, DTO)
- Naming conventions
- Configuration management rules
- Database access patterns
- Error handling guidelines
- Logging standards
- Testing requirements
- Security and performance guidelines

**When to read:**

- Before creating any new module or feature
- When unsure about where to place code
- When reviewing code for architecture compliance
- When onboarding new developers

**How to reference:**

- Cursor AI automatically reads this before coding (enforced by `.cursorrules`)
- Developers should read this before starting development
- Use as a reference during code reviews

#### 2. **Adding New Documentation**

**Common documentation files to add:**

```
docs/
├── architecture.md           # Architecture guide (existing)
├── api/                       # API documentation
│   ├── endpoints.md          # REST API endpoints
│   └── authentication.md     # Auth flow documentation
├── setup/                     # Setup guides
│   ├── installation.md       # Installation instructions
│   ├── configuration.md     # Environment configuration
│   └── deployment.md         # Deployment guide
├── development/              # Development guides
│   ├── contributing.md       # Contribution guidelines
│   └── coding-standards.md   # Coding standards
└── changes/                  # Change documentation
    └── CHANGELOG.md          # Version changelog
```

**Documentation Best Practices:**

- Keep documentation up-to-date with code changes
- Use clear, human-readable language
- Include code examples where helpful
- Add diagrams for complex concepts
- Link related documentation
- Update README.md to reference important docs

---

## 🎯 Quick Reference

### File/Folder Quick Guide

| File/Folder            | Purpose                  | When to Modify                               |
| ---------------------- | ------------------------ | -------------------------------------------- |
| `.cursor/commands/`    | Custom AI commands       | When adding new command templates            |
| `.cursor/rules.md`     | General coding rules     | When changing coding standards               |
| `.cursor/doc.md`       | Documentation standards  | When changing doc requirements               |
| `.cursorignore`        | Exclude files from AI    | When files slow down or shouldn't be indexed |
| `.cursorrules`         | Architecture enforcement | When adding new patterns or rules            |
| `docs/architecture.md` | Architecture guide       | When architecture changes                    |

### Common Workflows

**Creating a new module:**

1. Cursor AI reads `.cursorrules` → enforces architecture
2. References `docs/architecture.md` → follows structure
3. Creates files following module template
4. Applies rules from `.cursor/rules.md`

**Fixing an error:**

1. Use `/fix 'error description'` command
2. Cursor AI checks `.cursorrules` for compliance
3. Applies fixes following architecture guidelines

**Generating documentation:**

1. Use `/doc 'what to document'` command
2. Cursor AI follows `.cursor/doc.md` standards
3. Creates documentation in appropriate location

---

## 📝 Summary

- **`.cursor/`** - Customize Cursor AI behavior with commands and rules
- **`.cursorignore`** - Exclude files from AI processing for performance
- **`.cursorrules`** - Enforce architecture and coding standards automatically
- **`docs/`** - Store and maintain project documentation

All these files work together to ensure consistent, high-quality code that follows your project's architecture and standards.
