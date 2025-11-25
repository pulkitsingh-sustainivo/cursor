# Fix Command Handler

When this file is referenced with `/fix 'prompt'` or `@.cursor/commands/fix.md`, identify and fix errors based on the prompt.

## Behavior

1. **Read the user's prompt** - Extract what needs to be fixed
2. **Identify the issue** - Analyze errors, logs, or problematic code
3. **Locate root cause** - Find where the problem originates
4. **Apply fix** - Resolve the issue following best practices
5. **Verify solution** - Ensure the fix works and doesn't introduce new issues

## Example Usage

```
/fix The user creation endpoint is returning 500 error
```

This should:

- Check error logs or linter errors
- Identify the problematic code
- Fix the issue (missing validation, wrong type, etc.)
- Test the fix
- Ensure it follows architecture guidelines

## Fix Types

- **Runtime errors** - Application crashes, exceptions
- **Linter errors** - Code style, type errors
- **Logic errors** - Incorrect business logic
- **Architecture violations** - Code not following architecture.md
- **Dependency issues** - Missing imports, wrong dependencies
- **Type errors** - TypeScript type mismatches

## Rules

- Read error messages carefully
- Check linter output for issues
- Verify the fix doesn't break other functionality
- Follow architecture.md guidelines
- Use NestJS Logger for error handling
- Add proper error handling if missing
- Update tests if needed
