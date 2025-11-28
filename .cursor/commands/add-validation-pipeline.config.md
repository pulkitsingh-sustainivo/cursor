# Add Validation Pipeline Configuration

## Input

No input required - this command adds global validation pipeline using class-validator and class-transformer.

## Rules

- Validation must be applied globally in: `src/main.ts`
- Use `ValidationPipe` from `@nestjs/common`
- Must use `class-validator` and `class-transformer` packages
- Only use approved validation decorators:
  - `@IsString()`
  - `@IsNumber()`
  - `@IsUUID()`
  - `@IsBoolean()`
  - `@IsEmail()`
  - `@IsEnum()`
  - `@IsOptional()`
- Must use `@Type(() => ...)` for all required transformations
- All DTOs must have proper validation decorators
- Never use `console.log` - use NestJS Logger if needed

## Tasks to Perform

1. **Verify dependencies are installed**
   - Check if `class-validator` is installed (should already be installed)
   - Check if `class-transformer` is installed (should already be installed)
   - If not installed, run: `npm install class-validator class-transformer`

2. **Update main.ts**
   - Import `ValidationPipe` from `@nestjs/common`
   - Configure global ValidationPipe with options:
     ```typescript
     app.useGlobalPipes(
       new ValidationPipe({
         whitelist: true,                    // Strip properties that don't have decorators
         forbidNonWhitelisted: true,         // Throw error if non-whitelisted properties are present
         transform: true,                    // Automatically transform payloads to DTO instances
         transformOptions: {
           enableImplicitConversion: true,   // Enable implicit type conversion
         },
         validateCustomDecorators: true,     // Validate custom decorators
         disableErrorMessages: false,        // Enable error messages in production (set to true if needed)
       }),
     );
     ```
   - Place this after creating the app instance and before `app.listen()`

3. **Verify existing DTOs** (optional check)
   - Ensure all DTOs in `src/cat/dto/` use proper validation decorators
   - Ensure DTOs use `@Type()` decorator for transformations where needed
   - Example validation decorator usage:
     ```typescript
     import { IsString, IsNumber, IsOptional } from 'class-validator';
     import { Type } from 'class-transformer';

     export class CreateCatDto {
       @IsString()
       name: string;

       @IsNumber()
       @Type(() => Number)
       age: number;

       @IsOptional()
       @IsString()
       breed?: string;
     }
     ```

4. **Update DTOs if needed**
   - Add validation decorators to `CreateCatDto`, `UpdateCatDto`, etc. if they're missing
   - Add `@Type()` decorators for number/enum transformations
   - Ensure all required fields have proper validation

## ValidationPipe Configuration Options Explained

- `whitelist: true` - Automatically remove properties that don't have decorators
- `forbidNonWhitelisted: true` - Return error if non-whitelisted properties exist
- `transform: true` - Automatically transform payloads to DTO instances
- `transformOptions.enableImplicitConversion: true` - Convert string numbers to numbers automatically
- `validateCustomDecorators: true` - Enable validation for custom decorators
- `disableErrorMessages: false` - Show detailed error messages (set to true in production if needed)

## Error Response Format

When validation fails, the response will be:
```typescript
{
  statusCode: 400,
  message: ['property should not exist', 'name must be a string'],
  error: 'Bad Request'
}
```

## Output

1. Verified/installed `class-validator` and `class-transformer` packages
2. Updated `src/main.ts` with global ValidationPipe configuration
3. Global validation pipeline enabled for all endpoints
4. Automatic DTO transformation and validation
5. All incoming requests are validated against DTOs
6. Non-whitelisted properties are automatically stripped or rejected
7. Optional: Enhanced existing DTOs with proper validation decorators

