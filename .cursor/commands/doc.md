# Documentation Command Handler

When this file is referenced with `/doc 'prompt'` or `@.cursor/commands/doc.md`, generate or update documentation based on the prompt.

## Behavior

1. **Read the user's prompt** - Extract what documentation is needed
2. **Analyze the codebase** - Understand the structure and code to document
3. **Generate documentation** - Create comprehensive, human-readable documentation
4. **Update existing docs** - Modify existing documentation if needed

## Example Usage

```
/doc Document the user module API endpoints
```

This should:

- Analyze the user module controller
- Document all endpoints with request/response examples
- Include authentication requirements
- Add usage examples

## Documentation Types

- **API Documentation** - Endpoints, request/response formats
- **Code Documentation** - Function descriptions, parameters, return types
- **Architecture Documentation** - System design, module relationships
- **Setup Documentation** - Installation, configuration, deployment
- **README Updates** - Project overview, structure, usage

## Rules

- Write in clear, human-readable language
- Include code examples where relevant
- Keep documentation up-to-date with code
- Use markdown formatting
- Add diagrams or structure trees when helpful
