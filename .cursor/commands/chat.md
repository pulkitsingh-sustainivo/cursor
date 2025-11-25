# Chat Command Handler

When this file is referenced with `/chat 'prompt'` or `@.cursor/commands/chat.md`, treat the following prompt as a chat request and implement it.

## Behavior

1. **Read the user's prompt** - Extract the prompt from the command
2. **Apply chat context** - Use conversational AI assistance to understand and respond
3. **Implement the solution** - Create, modify, or explain code based on the prompt
4. **Follow architecture** - Ensure all implementations follow `/docs/architecture.md` guidelines

## Example Usage

```
/chat Create a user authentication module with JWT
```

This should:

- Understand the requirement
- Create the authentication module following NestJS architecture
- Implement JWT authentication
- Create all necessary files (controller, service, repository, DTOs, entities)

## Rules

- Always check architecture.md before implementing
- Use NestJS best practices
- Follow the module structure defined in architecture.md
- Implement complete solutions, not partial code
- Ask clarifying questions if the prompt is ambiguous
