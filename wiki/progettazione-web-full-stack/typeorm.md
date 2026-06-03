---
tags: [web, full-stack, backend, orm, database, postgresql, typeorm]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L5a_TypeORM.pdf
  - raw/L5b_Lab_TypeORM.pdf
---

# TypeORM e PostgreSQL

TypeORM è una libreria ORM (Object-Relational Mapping) per TypeScript che permette di lavorare con database relazionali usando classi e oggetti invece di scrivere SQL manualmente. Si integra nativamente con NestJS tramite `@nestjs/typeorm`.

## Punti chiave

- **Mapping OO→DB**: classe = tabella, oggetto = riga, proprietà = colonna
- **Decoratori**: `@Entity`, `@Column`, `@PrimaryGeneratedColumn`, `@OneToOne`, `@OneToMany`, `@ManyToMany` definiscono lo schema senza scrivere DDL
- **Una libreria per ogni tabella**: pattern architetturale del corso — ogni tabella ha la sua lib Nx con entity, repository, service, controller, DTO
- **`synchronize: true`** crea/aggiorna le tabelle automaticamente dall'entity (solo sviluppo — pericoloso in produzione)
- **`eager: true`** carica la relazione automaticamente; senza, bisogna specificare `relations: { ... }` nel `find()`
- **`cascade: true`** propaga le operazioni di insert/update/delete alla relazione

## Architettura multi-tier con DB

```
Front-end App (React)
      ↓
Back-end App (NestJS)
  ├── Business Logic Libs
  ├── ORM Libs (TypeORM)
  └── Database Connection Lib
      ↓
Database (PostgreSQL / MySQL / SQLite)
```

## Setup

```bash
# Installazione
npm i @nestjs/typeorm typeorm pg

# Creazione libreria di connessione
npx nx g @nx/nest:library libs/database --unitTestRunner=none
```

## Connessione al database (DatabaseModule)

```typescript
// libs/database/src/lib/database.module.ts
import { TypeOrmModule } from '@nestjs/typeorm';
import 'dotenv/config';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'postgres',
      host: process.env.PG_HOST ?? 'localhost',
      port: Number(process.env.PG_PORT ?? 5432),
      username: process.env.PG_USERNAME ?? 'postgres',
      password: process.env.PG_PASSWORD ?? 'postgres',
      database: process.env.PG_DATABASE ?? 'app',
      autoLoadEntities: true,   // carica le entità registrate nei moduli
      synchronize: true,         // NON usare in produzione
    }),
  ],
  exports: [TypeOrmModule],
})
export class DatabaseModule {}
```

Variabili d'ambiente nel file `.env` alla root del workspace: `PG_HOST`, `PG_PORT`, `PG_USERNAME`, `PG_PASSWORD`, `PG_DATABASE`.

## Entity e decoratori

Una **Entity** è una classe TypeScript annotata con `@Entity` che corrisponde a una tabella del DB.

```typescript
// users.entity.ts
import { Entity, PrimaryGeneratedColumn, Column } from 'typeorm';

@Entity('users')
export class UserEntity {
  @PrimaryGeneratedColumn()
  id: number;

  @Column({ type: 'varchar', length: 255, nullable: false })
  name: string;

  @Column({ type: 'varchar', length: 320, nullable: true })
  email: string | null;

  @Column({ type: 'enum', enum: UserRole, default: UserRole.USER })
  role: UserRole;
}
```

### Decoratori principali per le colonne

| Decoratore | Descrizione |
|---|---|
| `@PrimaryGeneratedColumn()` | Chiave primaria auto-incrementante |
| `@PrimaryColumn()` | Chiave primaria senza auto-increment |
| `@Column(options)` | Colonna normale |
| `@CreateDateColumn` | Data creazione (auto) |
| `@UpdateDateColumn` | Data ultimo aggiornamento (auto) |
| `@DeleteDateColumn` | Data eliminazione — abilita *soft-delete* |
| `@Column("simple-array")` | Array di valori in una sola colonna |
| `@Column("simple-json")` | Valore JSON serializzato (`JSON.stringify`) |

### Opzioni di `@Column`

`type`, `name`, `length`, `nullable`, `default`, `unique`, `select` (se includere nella SELECT * default).

## Relazioni tra entità

### Uno-a-uno (`@OneToOne`)

```typescript
// address.entity.ts
@Entity('addresses')
export class Address {
  @PrimaryGeneratedColumn() id: number;
  @Column({ type: 'varchar', length: 255 }) street: string;
  // ...
  @OneToOne(() => Author, (author) => author.address)
  author: Author;
}

// author.entity.ts
@Entity('authors')
export class Author {
  @PrimaryGeneratedColumn() id: number;
  @Column({ type: 'varchar', length: 255, nullable: false }) firstName: string;
  // ...
  @OneToOne(() => Address, (address) => address.author, { cascade: true, eager: true })
  @JoinColumn()   // lato che possiede la FK
  address: Address;
}
```

`@JoinColumn()` va sul lato che possiede la chiave esterna. `cascade: true` propaga le operazioni; `eager: true` carica l'address automaticamente.

### Uno-a-molti / Molti-a-uno (`@OneToMany` / `@ManyToOne`)

```typescript
// category.entity.ts
@Entity('categories')
export class Category {
  @PrimaryGeneratedColumn() id: number;
  @Column({ type: 'varchar', length: 255, nullable: false, unique: true }) name: string;
  @OneToMany(() => Book, (book) => book.category)
  books: Book[];
}

// book.entity.ts - lato Many
@ManyToOne(() => Category, (category) => category.books,
  { nullable: false, eager: true, onDelete: 'RESTRICT' })
@JoinColumn()
category: Category;
```

`onDelete: 'RESTRICT'` impedisce la cancellazione della categoria se esistono libri associati.

### Molti-a-molti (`@ManyToMany`)

```typescript
// book.entity.ts
@ManyToMany(() => Author, (author) => author.books, { cascade: false })
@JoinTable({
  name: 'books_authors',
  joinColumn: { name: 'book_id', referencedColumnName: 'id' },
  inverseJoinColumn: { name: 'author_id', referencedColumnName: 'id' }
})
authors: Author[];

// author.entity.ts
@ManyToMany(() => Book, (book) => book.authors)
books: Book[];
```

`@JoinTable` va su uno solo dei due lati (lato "owner"). Crea la tabella pivot `books_authors` automaticamente.

## Repository pattern

Ogni libreria espone un **repository** che implementa le operazioni CRUD sul DB:

```typescript
@Injectable()
export class UsersRepository {
  constructor(
    @InjectRepository(UserEntity)
    private readonly repository: Repository<UserEntity>
  ) {}

  async findAll(): Promise<UserEntity[]> {
    return this.repository.find();
  }

  async create(dto: CreateUserDto): Promise<UserEntity> {
    const user = this.repository.create(dto);
    return this.repository.save(user);
  }
}
```

Il service inietta il repository tramite `@InjectRepository(Entity)`. Per ottimizzare, il repository può essere iniettato direttamente nel service senza una classe Repository dedicata (si usa `TypeOrmModule.forFeature([...])`).

### Lettura con relazioni esplicite

```typescript
// Quando eager: false, specificare le relazioni nella query
async findAllBooks() {
  return this.bookRepository.find({
    relations: { authors: true }
  });
}
```

## Struttura libreria per tabella (in Nx)

```bash
# Crea libreria con service, controller, entity, repository, DTO
npx nx g @nx/nest:library libs/server/users --service -controller
npx nx g @nx/nest:class libs/server/users/src/lib/users.entity [--unitTestRunner=none]
npx nx g @nx/nest:class libs/server/users/src/lib/users.repository [--unitTestRunner=none]
npx nx g @nx/nest:class libs/server/users/src/lib/dto/create-user.dto [--unitTestRunner=none]
```

### Module della libreria

```typescript
@Module({
  imports: [TypeOrmModule.forFeature([Address, Author, Book, Category])],
  controllers: [OrgBooksController],
  providers: [OrgBooksService],
  exports: [OrgBooksService],
})
export class OrgBooksModule {}
```

`TypeOrmModule.forFeature([...])` registra le entità nel modulo e rende disponibili i repository tramite DI.

### Import nell'app principale

```typescript
@Module({
  imports: [ServerUsersModule, OrgBooksModule, DatabaseModule],
  ...
})
export class AppModule {}
```

## Approccio alla gestione delle relazioni (scelta del corso)

Il corso adotta un approccio **uno-a-uno tra librerie e tabelle** (massima modularità). Le conseguenze:
- I decorator `@OneToOne`, `@OneToMany`, `@ManyToMany` NON vengono usati sulle entity semplici: il vincolo referenziale è gestito a livello di *service* con FK esplicita
- Nel caso molti-a-molti, si crea una libreria aggiuntiva per la tabella pivot
- Nel laboratorio L5b, invece, vengono usati i decorator per le relazioni a scopo didattico

## Articoli correlati

- [[nodejs-nestjs]]
- [[typescript]]
- [[nx]]
- [[nestjs-auth]]

## Fonti

- `raw/L5a_TypeORM.pdf`
- `raw/L5b_Lab_TypeORM.pdf`
