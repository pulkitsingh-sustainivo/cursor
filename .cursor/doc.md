# Cursor Documentation Rules

## Rule: Documentation Standards
- Always document public APIs, classes, and interfaces with JSDoc comments.
- Include parameter descriptions, return types, and examples where helpful.
- Document complex business logic and algorithms with inline comments.
- Keep documentation up-to-date with code changes.

## Rule: Code Documentation Format
- Use JSDoc format for all function and class documentation.
- Include `@param` for all parameters with types and descriptions.
- Include `@returns` or `@return` for return values.
- Include `@throws` for exceptions that may be thrown.
- Include `@example` for complex functions when helpful.

## Rule: README Files
- Every module should have a README.md if it's complex or reusable.
- README should include: purpose, usage examples, API overview, and setup instructions.
- Keep README files concise and focused on essential information.
- Update README when adding new features or changing APIs.

## Rule: Change Documentation
- Document all significant changes in the `docs/changes/` folder.
- Use the changelog template format for consistency.
- Include: summary, files modified, breaking changes, and migration notes.
- Link to related issues or pull requests when applicable.

## Rule: API Documentation
- Document all REST endpoints with request/response examples.
- Include authentication requirements and error responses.
- Document query parameters, request bodies, and response schemas.
- Keep API documentation synchronized with actual implementation.

## Rule: Architecture Documentation
- Document architectural decisions and patterns used.
- Explain why certain patterns were chosen over alternatives.
- Include diagrams or structure descriptions for complex systems.
- Update architecture docs when making significant structural changes.

## Rule: Inline Comments
- Write self-documenting code first; add comments only when necessary.
- Explain "why" not "what" - the code should explain itself.
- Comment complex algorithms, business rules, and non-obvious logic.
- Avoid redundant comments that simply repeat the code.

## Rule: Type Documentation
- Document custom types, interfaces, and enums with clear descriptions.
- Explain the purpose and usage of complex type definitions.
- Include examples for generic types or complex type unions.
- Document type constraints and requirements.

## Rule: Error Documentation
- Document all custom exceptions and error types.
- Explain when and why errors are thrown.
- Include error codes and error response formats in API documentation.
- Provide guidance on error handling for consumers.

## Rule: Testing Documentation
- Document test setup and configuration requirements.
- Explain test data setup and mocking strategies.
- Document how to run tests and interpret results.
- Include examples of test cases for complex scenarios.
