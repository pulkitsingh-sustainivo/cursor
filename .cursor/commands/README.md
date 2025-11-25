# Cursor Commands Reference

## How to Use Command Files

Reference any command file using `@.cursor/commands/[filename]` in your Cursor chat to trigger the instructions in that file.

## Available Commands

### init.md
**Usage:** `@.cursor/commands/init.md`

Creates a complete production-ready NestJS project with:
- Full folder structure
- All configuration files
- Source code templates
- Docker setup
- Environment configuration
- Documentation

**Example:**
```
@.cursor/commands/init.md Create a new NestJS project called "tender-management"
```

### edit.md
**Usage:** `@.cursor/commands/edit.md`

Guidelines for editing code with Cursor.

### fix.md
**Usage:** `@.cursor/commands/fix.md`

Guidelines for fixing errors with Cursor.

### doc.md
**Usage:** `@.cursor/commands/doc.md`

Guidelines for generating documentation.

### chat.md
**Usage:** `@.cursor/commands/chat.md`

Guidelines for using chat functionality.

## Quick Start

To initialize a new NestJS project:

1. Open Cursor
2. In the chat, type: `@.cursor/commands/init.md Create a NestJS project`
3. Cursor will read the init.md file and create all necessary files
4. Follow the prompts to customize your project

