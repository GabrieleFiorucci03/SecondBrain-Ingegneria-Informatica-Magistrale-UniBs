---
tags: [web, full-stack, backend, nodejs, nestjs, rest-api, framework]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L4a_NodeNestJS.pdf
  - raw/L4b_Lab_NodeNestJS.pdf
---

# Node.js e NestJS – Sviluppo Backend

NestJS è un framework backend *opinionated* costruito su Node.js e TypeScript che impone una struttura chiara tramite moduli, controller e provider. Combina paradigmi OOP e funzionale/reattivo, ed è lo standard de-facto per backend TypeScript strutturati in Nx.

## Punti chiave

- **NestJS ≠ Nx**: Nx gestisce il monorepo e il tooling, NestJS è il framework applicativo backend — si usano insieme
- **Tre mattoni**: Module (composizione), Controller (rotte HTTP), Provider/Service (logica business)
- **Dependency Injection**: i provider vengono iniettati nei controller tramite il costruttore — NestJS risolve le dipendenze automaticamente
- **Decoratori**: `@Module()`, `@Controller()`, `@Injectable()`, `@Get()`, `@Post()`, ecc. definiscono il comportamento a livello di metadati
- **Swagger**: integrazione nativa per esporre documentazione interattiva delle API a `/api/docs`
- **async/await**: NestJS usa Promises ovunque; `async` marca una funzione non-bloccante, `await` sospende finché la Promise si risolve

## NestJS vs framework simili

| Framework | Tipo | Note |
|---|---|---|
| **NestJS** | opinionated, TypeScript | costruito su Express; struttura obbligata |
| **Express** | unopinionated, JS/TS | nessuna struttura — NestJS ci è sopra |
| **NextJS** | React SSR | frontend framework, non backend puro |

## I tre componenti fondamentali

### Controller
Espone rotte HTTP e riceve le richieste. Delega la logica ai provider e restituisce la risposta.

```typescript
// app.controller.ts
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get('data')
  getData() {
    return this.appService.getData();
  }
}
```

`@Controller()` — classe come controller; `@Get('data')` — mappa GET /api/data. Il servizio viene iniettato nel costruttore (dependency injection).

### Provider (Service)
Incapsula logica di business riutilizzabile. Marcato con `@Injectable()` per essere iniettabile.

```typescript
// app.service.ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class AppService {
  getData(): { message: string } {
    return { message: 'Hello API' };
  }
}
```

Servizi, repository, factories, helpers possono essere implementati come provider.

### Module
Organizza l'app in unità funzionali, dichiarando controller e provider, e definendo import/export.

```typescript
// app.module.ts
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

## Bootstrapping (entry point)

```typescript
// main.ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app/app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.setGlobalPrefix('api');          // tutte le rotte hanno prefisso /api
  const port = process.env.PORT || 3000;
  await app.listen(port);
}
bootstrap();
```

- `NestFactory.create(AppModule)` costruisce il grafo moduli/controller/provider
- `setGlobalPrefix('api')` → ogni rotta ha prefisso `/api`
- `process.env.PORT || 3000` → legge da variabile d'ambiente (con `dotenv`)

### Variabili d'ambiente
```bash
npx nx add dotenv
```
File `.env` nella root del workspace: `PORT=3333`. In `main.ts`: `import 'dotenv/config';`

## Setup in Nx

```bash
# Installare plugin NestJS
npx nx add @nx/nest

# Creare una nuova app NestJS
npx nx g @nx/nest:app apps/{nome_app}

# Creare una libreria NestJS con controller e service
npx nx g @nx/nest:library libs/server/users --controller --service
# Con import alias personalizzato
npx nx g @nx/nest:library libs/server/users --importPath=@server/users

# Avviare il server
npx nx serve {appName}   # porta 3000 di default
```

Struttura generata per ogni progetto NestJS in Nx:
```
apps/{nome}/src/
├── main.ts               # entry point
└── app/
    ├── app.module.ts
    ├── app.controller.ts
    ├── app.controller.spec.ts
    ├── app.service.ts
    └── app.service.spec.ts
```

## Swagger (documentazione API)

```bash
npx nx add @nestjs/swagger
```

```typescript
// in main.ts
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';

const config = new DocumentBuilder().build();
const document = SwaggerModule.createDocument(app, config);
SwaggerModule.setup('api/docs', app, document);
```

L'interfaccia web è disponibile a `http://<host>:<port>/api/docs` e permette di vedere e invocare le API.

Per raggruppare le rotte in Swagger aggiungere `@ApiTags('nome-gruppo')` al controller.

## Testing in NestJS

NestJS genera automaticamente file `.spec.ts` per controller e service:

```typescript
// app.controller.spec.ts
import { Test, TestingModule } from '@nestjs/testing';

describe('AppController', () => {
  let app: TestingModule;

  beforeAll(async () => {
    app = await Test.createTestingModule({
      controllers: [AppController],
      providers: [AppService],
    }).compile();   // funzione asincrona
  });

  it('should return "Hello API"', () => {
    const controller = app.get<AppController>(AppController);
    expect(controller.getData()).toEqual({ message: 'Hello API' });
  });
});
```

`@nestjs/testing` fornisce `Test.createTestingModule` per creare un modulo finto di test. `beforeAll` esegue il setup una sola volta per tutta la suite.

## Routing avanzato

```typescript
@Controller('users')
export class UsersController {
  @Get()                           // GET /users
  getUsers(@Query('role') role?: 'USER' | 'ADMIN') { ... }

  @Get(':id')                      // GET /users/:id
  getOneUser(@Param('id', ParseIntPipe) id: number) { ... }

  @Post()                          // POST /users
  create(@Body(ValidationPipe) user: CreateUserDto) { ... }

  @Patch(':id')                    // PATCH /users/:id
  update(@Param('id') id: string, @Body() update: {}) { ... }
}
```

**Attenzione all'ordine**: le rotte vengono valutate nell'ordine di dichiarazione. `@Get(':id')` prima di `@Get('interns')` fa sì che `interns` venga trattato come id → invertire l'ordine.

**Decoratori parametro chiave**:
- `@Param('name')` — path parameter
- `@Query('name')` — query string parameter
- `@Body()` — corpo della richiesta HTTP

## Pipes

Le **pipes** sono un tipo particolare di middleware che operano prima del route handler. Implementano `PipeTransform`.

**Usi principali**: *Transformation* (converti tipo) e *Validation* (valida input).

Pipes built-in: `ParseIntPipe`, `ParseFloatPipe`, `ParseBoolPipe`, `ParseArrayPipe`, `ParseUUIDPipe`, `ParseEnumPipe`, `DefaultValuePipe`, `ParseFilePipe`, `ParseDatePipe`, `ValidationPipe`.

```typescript
// Transformation: converte il param stringa in number
@Get(':id')
getOneUser(@Param('id', ParseIntPipe) id: number) { ... }

// Validation: valida il body tramite class-validator
@Post()
create(@Body(ValidationPipe) user: CreateUserDto) { ... }
```

## DTO (Data Transfer Object)

I DTO definiscono lo schema degli input delle API:

```typescript
// dto/create-user.dto.ts
import { IsEmail, IsEnum, IsNotEmpty, IsString } from 'class-validator';

export enum UserRole { USER = 'USER', ADMIN = 'ADMIN' }

export class CreateUserDto {
  @IsString()
  @IsNotEmpty()
  name: string;

  @IsEmail()
  email: string;

  @IsEnum(UserRole, { message: 'Valid role required among USER or ADMIN' })
  role: UserRole;
}

// UpdateUserDto eredita tutto da CreateUserDto ma con campi opzionali
import { PartialType } from '@nestjs/mapped-types';
export class UpdateUserDto extends PartialType(CreateUserDto) {}
```

Installazione: `npm i class-validator class-transformer`. Se TS dà errori di inizializzazione, aggiungere `"strict": false` in `tsconfig.lib.json`.

## HTTP Exceptions

NestJS gestisce le eccezioni non catturate tramite un *exceptions layer* built-in che invia automaticamente una risposta HTTP appropriata.

```typescript
import { NotFoundException } from '@nestjs/common';

getOneUser(id: number) {
  const user = this.users.find(u => u.id === id);
  if (!user) throw new NotFoundException('User not found!');
  return user;
}
```

Eccezioni HTTP disponibili: `BadRequestException` (400), `UnauthorizedException` (401), `NotFoundException` (404), `ForbiddenException` (403), `ConflictException` (409), `InternalServerErrorException` (500), e altre.

Per eccezioni personalizzate, si estende `HttpException` o si usano *Exception Filters*.

## CJS vs ESM (note di compatibilità)

- **Node.js 2009** adottò **CommonJS (CJS)**: moduli caricati a runtime con `require()`
- **ECMAScript** introduce **ES Modules (ESM)**: `import`/`export` statici, noti prima dell'esecuzione → tree shaking migliore
- In TypeScript si scrive sempre con `import` (stile ESM)
- Dopo la compilazione, il modulo output dipende dal campo `"type"` in `package.json`:
  - `"type": "commonjs"` → NestJS default
  - `"type": "module"` → ESM
- ESM può importare CJS; CJS ha problemi a usare librerie pure ESM

## Articoli correlati

- [[typescript]]
- [[nx]]
- [[typeorm]]
- [[nestjs-auth]]
- [[introduzione-al-corso]]

## Fonti

- `raw/L4a_NodeNestJS.pdf`
- `raw/L4b_Lab_NodeNestJS.pdf`
