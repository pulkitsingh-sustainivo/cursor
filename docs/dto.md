# Class-Validator + Class-Transformer Architecture Prompt

Generate DTOs using industry-standard NestJS + TypeORM + class-validator + class-transformer best practices.

## GENERAL RULES
- All DTOs must use `class-validator` + `class-transformer`.
- Use strict typing for every field.
- Do not include fields not present in the entity.
- Enforce validation rules based on field type.
- Use decorators in the correct order: `@ApiProperty()` → `validators` → `transformers`.
- Never expose internal fields like id, timestamps, or isDeleted unless explicitly required.

---

## VALIDATION RULES (class-validator)
### Strings
- `@IsString()`
- `@IsNotEmpty()` for required fields
- `@IsOptional()` for update DTOs
- `@Length(min, max)` where relevant

### Numbers
- `@IsInt()` or `@IsNumber()`
- `@Min()` / `@Max()`
- Always validate relation IDs (e.g., `userId`) as numbers

### Booleans
- `@IsBoolean()`

### Arrays
- `@IsArray()`
- `@ArrayMinSize(1)`
- Use `@IsString({ each: true })` or correct type per item

### Dates
- `@IsDateString()`

### Enums
- `@IsEnum(EnumName)`

---

## TRANSFORMATION RULES (class-transformer)
- Use `@Type(() => Number)` for numeric IDs
- Use `@Type(() => Date)` for timestamps
- Use `@Exclude()` to hide private/internal fields
- Use `@Expose()` to explicitly specify fields in responses
- Transform nested objects using:
  `@Type(() => NestedDto)`

---

## DTO STRUCTURE RULES
### CreateDTO
- All required fields included
- No optional business-critical fields
- Must validate strictly

### UpdateDTO
- Every field must use `@IsOptional()`
- Must allow partial updates

### ResponseDTO
- Use `@Expose()` for every property
- Use transformer rules for n
