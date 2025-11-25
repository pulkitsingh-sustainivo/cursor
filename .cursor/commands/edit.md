# Edit Command Handler

When this file is referenced with `/edit 'prompt'` or `@.cursor/commands/edit.md`, edit existing code based on the prompt.

## Behavior

1. **Read the user's prompt** - Extract what needs to be edited
2. **Locate the target code** - Find the files and code sections to modify
3. **Understand context** - Read surrounding code to maintain consistency
4. **Apply changes** - Make the requested edits following architecture guidelines
5. **Verify changes** - Ensure edits don't break existing functionality

## Example Usage

```
/edit Add pagination to the user list endpoint
```

This should:

- Find the user controller and service
- Add pagination query parameters
- Update the repository to support pagination
- Modify response DTOs to include pagination metadata

## Edit Types

- **Feature additions** - Add new functionality to existing code
- **Refactoring** - Improve code structure without changing behavior
- **Bug fixes** - Correct errors in existing code
- **Optimizations** - Improve performance or efficiency
- **Code style** - Align with project conventions

## Rules

- Always read the file before editing
- Maintain existing code style and patterns
- Follow architecture.md guidelines strictly
- Preserve existing functionality unless explicitly asked to change
- Update related files (DTOs, tests) if needed
- Check for breaking changes
