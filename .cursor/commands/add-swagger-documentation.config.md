# Add Swagger Documentation Configuration

## Input

No input required - this command adds Swagger/OpenAPI documentation to the entire application.

## Rules

- Swagger configuration must be in: `src/main.ts`
- Use `@nestjs/swagger` package
- Use `DocumentBuilder` for configuration
- Use tags for organizing modules
- All DTOs must have `@ApiProperty()` decorators
- Hide internal/private APIs from documentation
- Use kebab-case for tags (e.g., `cat`, `user-management`)

## Tasks to Perform

1. **Install required dependencies**
   - Install `@nestjs/swagger` package: `npm install @nestjs/swagger`
   - This will automatically include `swagger-ui-express` and `swagger-jsdoc` as dependencies

2. **Update main.ts**
   - Import `SwaggerModule` and `DocumentBuilder` from `@nestjs/swagger`
   - Create Swagger document using `DocumentBuilder` with:
     - `.setTitle()` - Application title
     - `.setDescription()` - Application description
     - `.setVersion()` - API version (e.g., '1.0')
     - `.addTag()` - Add tags for each module (at minimum: 'default' or 'app')
     - `.addBearerAuth()` - Add JWT bearer auth support (optional but recommended)
     - `.addServer()` - Add server URLs (optional)
     - `.build()` - Build the document
   - Setup Swagger: `SwaggerModule.setup('api', app, document)`
   - Place Swagger setup before `app.listen()`
   - Access Swagger UI at: `http://localhost:3000/api`

3. **Configure DTOs for Swagger** (if needed)
   - All existing DTOs should have `@ApiProperty()` decorators on fields
   - Example:
     ```typescript
     @ApiProperty({ description: 'Cat name', example: 'Fluffy' })
     @IsString()
     name: string;
     ```
   - Use `@ApiPropertyOptional()` for optional fields
   - Use `@ApiHideProperty()` to hide properties from documentation

4. **Configure Controllers for Swagger**
   - Add `@ApiTags('module-name')` to controllers (e.g., `@ApiTags('cat')`)
   - Add `@ApiOperation()` to describe endpoints
   - Add `@ApiResponse()` to document response types
   - Use `@ApiBearerAuth()` for authenticated endpoints

5. **Update existing controllers** (optional enhancement)
   - Add Swagger decorators to `src/cat/controllers/cat.controller.ts`:
     - `@ApiTags('cat')` on controller class
     - `@ApiOperation()` on each endpoint method
     - `@ApiResponse()` for success and error responses

## Swagger Configuration Example

```typescript
const config = new DocumentBuilder()
  .setTitle('My NestJS Application')
  .setDescription('API Documentation for My NestJS Application')
  .setVersion('1.0')
  .addTag('cat')
  .addBearerAuth()
  .build();
const document = SwaggerModule.createDocument(app, config);
SwaggerModule.setup('api', app, document);
```

## Output

1. Installed `@nestjs/swagger` package
2. Updated `src/main.ts` with Swagger configuration
3. Swagger UI accessible at `http://localhost:3000/api`
4. API documentation automatically generated from DTOs and controllers
5. Optional: Enhanced existing controllers with Swagger decorators

