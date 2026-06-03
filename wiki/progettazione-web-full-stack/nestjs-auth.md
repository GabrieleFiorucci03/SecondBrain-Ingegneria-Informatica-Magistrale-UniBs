---
tags: [web, full-stack, backend, auth, jwt, passport, sicurezza]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L6_NestJS_auth.pdf
---

# Autenticazione e Autorizzazione in NestJS

NestJS gestisce autenticazione e autorizzazione tramite **Passport**, una libreria middleware modulare che supporta diverse *strategie*. Il flusso standard del corso prevede: registrazione/login con credenziali (email+password) → emissione di un **JWT** → protezione delle rotte tramite **guard** che verificano il token.

## Punti chiave

- **Passport**: si occupa di «chi è l'utente»; NestJS organizza l'applicazione intorno ad esso
- **Due strategie**: `passport-local` (credenziali) e `passport-jwt` (token)
- **bcrypt**: le password non si salvano mai in chiaro — si salva il `passwordHash`
- **JWT**: token firmato con `SECRET_KEY` che il client include nell'`Authorization: Bearer <token>` header
- **Guard**: componente NestJS che protegge una rotta verificando il token prima che la richiesta raggiunga il controller
- **Tre librerie Nx**: `users` (gestione utenti + DB), `auth` (strategie + login), `security` (guard condivisi) — separazione per evitare dipendenze circolari

## Pacchetti richiesti

```bash
npm install @nestjs/passport passport passport-local
npm install @nestjs/jwt passport-jwt
npm install bcrypt
npm install -D @types/passport-jwt @types/bcrypt @types/passport-local
```

| Package | Ruolo |
|---|---|
| `passport` | Core engine autenticazione |
| `passport-local` | Strategia login con credenziali |
| `passport-jwt` | Strategia autenticazione con JWT |
| `@nestjs/passport` | Integrazione NestJS (guard + strategia) |
| `@nestjs/jwt` | Gestione token JWT |
| `bcrypt` | Hash e verifica password |

## Architettura modulare

```
AUTH MODULE       USERS MODULE
    |                  |
    └──────┬───────────┘
           ↓
    SECURITY MODULE
       (Guards)
```

- **`@server/users`**: entità `UserEntity`, repository, service (CRUD utenti)
- **`@server/auth`**: strategie Passport, `ServerAuthService`, controller `/auth`
- **`@server/security`**: guard condivisi (JwtAuthGuard) importati da tutte le app

Librerie separate per evitare dipendenze circolari tra `auth` e `users`.

## Aggiornamento UserEntity per l'auth

```typescript
@Entity('users')
export class UserEntity {
  @PrimaryGeneratedColumn() id: number;
  @Column({ type: 'varchar', length: 255, nullable: false }) name: string;
  @Column({ type: 'varchar', length: 320, nullable: false, unique: true }) email: string;
  @Column() passwordHash: string;   // MAI la password in chiaro
  @Column({ type: 'enum', enum: UserRole, default: UserRole.USER }) role: UserRole;
}
```

## Flusso di registrazione

```
POST /auth/register (RegisterDto)
  → ServerAuthController
    → ServerAuthService.register(dto)
      → ServerUsersService.create(dto)  → salva utente con passwordHash
      ← AuthenticatedUser
    → ServerAuthService.login(user)     → genera JWT
    ← AuthResponse { access_token, user }
```

```typescript
// auth.service.ts
async register(dto: RegisterDto): Promise<AuthResponse> {
  const user = await this.usersService.create(dto);
  const { passwordHash, ...result } = user;
  return this.login(result);
}

async login(user: AuthenticatedUser): Promise<AuthResponse> {
  const payload = { sub: user.id, name: user.name, email: user.email, role: user.role };
  return { access_token: await this.jwtService.signAsync(payload), user };
}
```

```typescript
// users.service.ts – hashing della password
async create(dto: CreateUserDto): Promise<UserEntity> {
  const existing = await this.usersRepository.findByEmail(dto.email);
  if (existing) throw new ConflictException('Email already in use');
  const passwordHash = await bcrypt.hash(dto.password, 10);
  return this.usersRepository.createOne(dto, passwordHash);
}
```

## Interfacce chiave

```typescript
export interface AuthenticatedUser {
  id: number;
  email: string;
  role: UserRole;
  name: string;
}

export class AuthResponse {
  access_token: string;
  user: AuthenticatedUser;
}

export interface JwtPayload {
  sub: number;
  email: string;
  role: UserRole;
  name: string;
}
```

## Strategia local (passport-local)

Verifica email + password al momento del login:

```typescript
// local.strategy.ts
@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private authService: ServerAuthService) {
    super({ usernameField: 'email' });  // default è 'username'
  }

  async validate(email: string, password: string): Promise<AuthenticatedUser> {
    return this.authService.validateUser(email, password);
  }
}

// auth.service.ts
async validateUser(email: string, password: string): Promise<AuthenticatedUser> {
  const user = await this.usersService.findByEmail(email);
  if (!user) throw new NotFoundException('Credentials not valid');
  const passwordMatches = await bcrypt.compare(password, user.passwordHash);
  if (!passwordMatches) throw new UnauthorizedException('Credentials not valid!');
  const { passwordHash, ...result } = user;
  return result;
}
```

## Auth controller (login)

```typescript
@UseGuards(LocalAuthGuard)
@Post('login')
login(@Request() req: RequestWithUser) {
  return this.serverAuthService.login(req.user);
}

// LocalAuthGuard — wrapper per evitare magic strings
@Injectable()
export class LocalAuthGuard extends AuthGuard('local') {}
```

`@UseGuards(LocalAuthGuard)` attiva la `LocalStrategy` prima che il controller venga eseguito. Se la validazione fallisce, Passport ritorna 401 automaticamente.

## Strategia JWT (passport-jwt)

Verifica il token nelle richieste successive al login:

```typescript
// jwt.strategy.ts
@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    const secret = process.env.SECRET_KEY;
    if (!secret) throw new Error('SECRET_KEY is not defined');
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: secret,
    });
  }

  validate(payload: JwtPayload): AuthenticatedUser {
    return {
      id: payload.sub,
      email: payload.email,
      role: payload.role,
      name: payload.name
    };
  }
}
```

La chiave segreta (`SECRET_KEY`) va in `.env` e NON deve mai essere committata. Il token viene estratto dall'header `Authorization: Bearer <token>`.

## Security module (guard condivisi)

```typescript
// libs/server/security
@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}

// Utilizzo in qualsiasi controller protetto
@UseGuards(JwtAuthGuard)
@Get('profile')
getProfile(@Request() req) {
  return req.user;  // AuthenticatedUser iniettato da JwtStrategy
}
```

Il security module importa sia `AuthModule` (per `JwtStrategy`) che `UsersModule` (per `UsersRepository`), evitando che `auth` e `users` si importino a vicenda.

## Validazione password nel DTO

```typescript
export class CreateUserDto {
  @IsString() @IsNotEmpty() name: string;
  @IsEmail() @IsNotEmpty() email: string;

  @IsString() @IsNotEmpty()
  @MinLength(8)
  @Matches(/[A-Z]/, { message: 'Password must contain at least one uppercase letter' })
  @Matches(/[?^!#@]/, { message: 'Password must contain at least one symbol' })
  password: string;
}
```

## Auth module completo

```typescript
@Module({
  imports: [
    ServerUsersModule,
    PassportModule,
    JwtModule.register({
      secret: process.env.SECRET_KEY,
      signOptions: { expiresIn: '24h' }
    }),
  ],
  controllers: [ServerAuthController],
  providers: [ServerAuthService, LocalStrategy, JwtStrategy],
  exports: [ServerAuthService],
})
export class ServerAuthModule {}
```

I token JWT scadono dopo 24h (`expiresIn: '24h'`). Per refresh token: implementare una rotta `/auth/refresh` separata.

## Tabella: cosa va dove nel modulo

| Tipo | `@Injectable` | Va in `exports` | Perché |
|---|---|---|---|
| Service | Sì | Sì | usato via DI |
| Repository | Sì | A volte | data access |
| Strategy | Sì | No | usata da Passport |
| Guard | Sì | No | usata via decoratore |
| DTO | No | No | non è un provider |

## Rotte protette e autorizzazione per ruolo

```typescript
// users.controller.ts — rotta protetta + filtro per ruolo
@Get()
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles(UserRole.ADMIN)
@ApiBearerAuth()
getUsers(@Query('role', new ParseEnumPipe(UserRole, { optional: true })) role?: UserRole) {
  return this.serverUsersService.getUsers(role);
}

// Rotta con utente corrente
@Get('me')
@UseGuards(JwtAuthGuard)
@ApiBearerAuth()
getMe(@CurrentUser() user: unknown) {
  return user;
}
```

## Security module – guard e decorator

Il **security module** (`@server/security`) esporta i guard e i decorator riusabili in tutta l'app:

```typescript
// jwt-auth.guard.ts
@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}

// roles.guard.ts — autorizzazione per ruolo
@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.getAllAndOverride<UserRole[]>(
      ROLES_KEY,
      [context.getHandler(), context.getClass()]
    );
    if (!requiredRoles || requiredRoles.length === 0) return true;
    const request = context.switchToHttp().getRequest();
    const user = request.user;
    if (!user) throw new ForbiddenException('User not found in request');
    if (!requiredRoles.includes(user.role))
      throw new ForbiddenException('Insufficient permissions');
    return true;
  }
}

// roles.decorator.ts
export const ROLES_KEY = 'roles';
export const Roles = (...roles: UserRole[]) => SetMetadata(ROLES_KEY, roles);

// current-user.decorator.ts
export const CurrentUser = createParamDecorator(
  (_data: unknown, ctx: ExecutionContext) => ctx.switchToHttp().getRequest().user
);
```

## Swagger con Bearer Auth

```typescript
// main.ts
const config = new DocumentBuilder()
  .addBearerAuth()     // aggiunge il pulsante "Authorize" in Swagger UI
  .setTitle('Nx-NestJS')
  .setDescription('API DOCUMENTATION')
  .setVersion('1.0')
  .build();
```

`@ApiBearerAuth()` sul controller/endpoint indica che quella rotta richiede il token nel Swagger UI.

## Articoli correlati

- [[nodejs-nestjs]]
- [[typeorm]]
- [[typescript]]

## Fonti

- `raw/L6_NestJS_auth.pdf`
