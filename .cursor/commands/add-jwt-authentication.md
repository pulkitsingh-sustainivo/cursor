# Add JWT Authentication Configuration

## Input

No input required - this command adds JWT authentication to the entire application.

## Rules

- Create authentication module in: `src/auth/`
- Use kebab-case for files.
- Use PascalCase for classes.
- Use camelCase for variables.
- All JWT configuration must use ConfigModule (never use process.env directly)
- JWT secret must be stored in environment variables
- Guards must be composable
- AuthGuard validates JWT tokens

## Folder Structure to Generate

src/auth/
├── auth.module.ts
├── auth.service.ts
├── strategies/
│   └── jwt.strategy.ts
├── guards/
│   ├── jwt-auth.guard.ts
│   └── roles.guard.ts
├── decorators/
│   ├── current-user.decorator.ts
│   └── roles.decorator.ts
├── dto/
│   ├── login.dto.ts
│   └── register.dto.ts
├── interfaces/
│   └── jwt-payload.interface.ts
└── constants/
    └── auth.constants.ts

## Tasks to Perform

1. **Install required dependencies**
   - Install JWT packages: `npm install @nestjs/jwt @nestjs/passport passport passport-jwt`
   - Install types: `npm install -D @types/passport-jwt`
   - Install bcrypt for password hashing: `npm install bcrypt`
   - Install types: `npm install -D @types/bcrypt`

2. **Create JWT configuration** (if ConfigModule exists)
   - Create or update `src/config/jwt.config.ts`:
     - Export function that returns JWT configuration using ConfigService
     - Include: secret, expiresIn (e.g., '1d', '7d')
     - Use ConfigService.get() for all JWT settings

3. **Create authentication constants**
   - Create `src/auth/constants/auth.constants.ts`:
     - Export JWT constants (if not using ConfigModule)
     - Define default values

4. **Create JWT payload interface**
   - Create `src/auth/interfaces/jwt-payload.interface.ts`:
     - Define interface with: sub (user id), email, roles (optional)

5. **Create JWT Strategy**
   - Create `src/auth/strategies/jwt.strategy.ts`:
     - Extend `PassportStrategy(Strategy)` from `@nestjs/passport`
     - Inject ConfigService (or use constants)
     - Implement `validate()` method that returns user payload
     - Extract token from Authorization header (Bearer token)

6. **Create JWT Auth Guard**
   - Create `src/auth/guards/jwt-auth.guard.ts`:
     - Extend `AuthGuard('jwt')` from `@nestjs/passport`
     - Can be used with `@UseGuards(JwtAuthGuard)` decorator

7. **Create Roles Decorator**
   - Create `src/auth/decorators/roles.decorator.ts`:
     - Create custom decorator using `SetMetadata('roles', roles)`
     - Usage: `@Roles('admin', 'user')`

8. **Create Roles Guard**
   - Create `src/auth/guards/roles.guard.ts`:
     - Extend `CanActivate` from `@nestjs/common`
     - Check if user has required roles
     - Work with Roles decorator

9. **Create Current User Decorator**
   - Create `src/auth/decorators/current-user.decorator.ts`:
     - Create custom parameter decorator using `createParamDecorator`
     - Extract user from request object
     - Usage: `@CurrentUser() user: User`

10. **Create Auth DTOs**
    - Create `src/auth/dto/login.dto.ts`:
      - `LoginDto` with email and password fields
      - Use validation decorators (@IsEmail(), @IsString())
      - Add @ApiProperty() for Swagger
    - Create `src/auth/dto/register.dto.ts`:
      - `RegisterDto` with email, password, and optional fields
      - Use validation decorators
      - Add @ApiProperty() for Swagger

11. **Create Auth Service**
    - Create `src/auth/auth.service.ts`:
      - Inject JwtService from `@nestjs/jwt`
      - Implement `login()` method: validate credentials, return JWT token
      - Implement `register()` method: hash password, create user, return JWT token
      - Implement `validateUser()` method: validate credentials
      - Use bcrypt for password hashing/comparison
      - Return tokens with payload

12. **Create Auth Controller**
    - Create `src/auth/auth.controller.ts`:
      - POST `/auth/login` endpoint
      - POST `/auth/register` endpoint
      - Add Swagger decorators (@ApiTags('auth'))
      - Use DTOs for validation

13. **Create Auth Module**
    - Create `src/auth/auth.module.ts`:
      - Import JwtModule.registerAsync() with ConfigService
      - Import PassportModule
      - Provide: AuthService, JwtStrategy
      - Export: AuthService, JwtAuthGuard

14. **Update app.module.ts**
    - Import AuthModule
    - Add AuthModule to imports array

15. **Update main.ts** (if ConfigModule exists)
    - Ensure ConfigModule is set up before AuthModule

16. **Create .env.example** (if not exists)
    - Add JWT configuration:
      ```
      JWT_SECRET=your-secret-key-here
      JWT_EXPIRES_IN=1d
      ```

## JWT Configuration Pattern

```typescript
// src/config/jwt.config.ts
export const jwtConfig = (configService: ConfigService) => ({
  secret: configService.get('JWT_SECRET'),
  signOptions: {
    expiresIn: configService.get('JWT_EXPIRES_IN') || '1d',
  },
});
```

## JWT Strategy Pattern

```typescript
// src/auth/strategies/jwt.strategy.ts
@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(
    private configService: ConfigService,
    // Inject user service/repository if needed
  ) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: configService.get('JWT_SECRET'),
    });
  }

  async validate(payload: JwtPayload) {
    // Return user object or payload
    return { userId: payload.sub, email: payload.email };
  }
}
```

## Auth Service Pattern

```typescript
// src/auth/auth.service.ts
@Injectable()
export class AuthService {
  constructor(
    private jwtService: JwtService,
    // Inject user service/repository
  ) {}

  async login(loginDto: LoginDto) {
    // Validate user
    // Generate JWT token
    return {
      access_token: this.jwtService.sign(payload),
    };
  }

  async register(registerDto: RegisterDto) {
    // Hash password
    // Create user
    // Generate JWT token
  }
}
```

## Usage Examples

1. **Protect routes with JWT:**
   ```typescript
   @UseGuards(JwtAuthGuard)
   @Get('profile')
   getProfile(@CurrentUser() user: User) {
     return user;
   }
   ```

2. **Protect routes with roles:**
   ```typescript
   @UseGuards(JwtAuthGuard, RolesGuard)
   @Roles('admin')
   @Delete(':id')
   deleteUser() {
     // Only admins can access
   }
   ```

## Output

1. Installed JWT and authentication packages (`@nestjs/jwt`, `@nestjs/passport`, `passport`, `passport-jwt`, `bcrypt`)
2. Created `src/auth/` module structure with all required files
3. Created JWT strategy for token validation
4. Created JWT auth guard for protecting routes
5. Created roles guard and decorator for RBAC
6. Created current user decorator for extracting user from request
7. Created login and register DTOs with validation
8. Created auth service with login and register methods
9. Created auth controller with login and register endpoints
10. Created auth module with JWT configuration
11. Updated `app.module.ts` to import AuthModule
12. Optional: Created JWT config file using ConfigModule
13. Optional: Created `.env.example` with JWT configuration

