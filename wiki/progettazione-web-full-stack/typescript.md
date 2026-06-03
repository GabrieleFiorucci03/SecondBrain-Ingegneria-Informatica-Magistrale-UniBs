---
tags: [web, full-stack, typescript, linguaggi, tipizzazione]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L3a_TypeScript.pdf
  - raw/L3b_Lab_TypeScript.pdf
---

# TypeScript

TypeScript è un superset tipizzato di JavaScript che compila in plain JavaScript. Nato per rispondere ai limiti di JS in applicazioni grandi e longeve, aggiunge un sistema di tipi a compile-time mantenendo piena compatibilità con l'ecosistema JS esistente. È il «collante» dello stack full-stack moderno: usato su Node.js (backend) e React (frontend).

## Punti chiave

- **Typed superset**: ogni file JS valido è TS valido; si aggiungono annotazioni di tipo opzionali
- **Errori a compile-time**: i bug di tipo si scoprono prima dell'esecuzione, non a runtime
- **Type inference**: il compilatore deduce spesso il tipo senza annotazione esplicita
- **Collante dello stack**: condivide tipi tra backend (NestJS) e frontend (React), abilita strumenti come [[trpc]]
- Attualmente alla versione **5.9**; open source, gira su qualsiasi browser/host/OS
- `any` disabilita la type safety; `unknown` la massimizza (richiede narrowing); preferire `unknown` a `any`

## Perché TypeScript (storia)

- Anni 2000: JS usato per validazioni semplici, logica sul server
- 2004–2006: AJAX e Rich Internet Applications alzano le aspettative
- 2010+: HTML5, app client-centriche sempre più complesse; JS standardizzato (ES6/2015)
- Problema: JS «scala male» → mancanza di tipi, API implicite, scalabilità del codice difficile
- Soluzioni pre-TS: prototype/module pattern, Script# (C# → JS), CoffeeScript → tutti con compromessi
- **TypeScript** (Microsoft, open source): estensione di JS per applicazioni di scala

## Dichiarazione delle variabili

| Keyword | Scope | Riassegnabile | Note |
|---|---|---|---|
| `const` | block `{…}` | No | ideale se il riferimento non cambia |
| `let` | block `{…}` | Sì | contatori, accumulatori |
| `var` | function | Sì | pre-ES6; *hoisting* → `undefined` fino all'assegnazione; evitare |

## Tipi primitivi e speciali

### Primitivi
- `boolean` — true/false
- `number` — tutti i numerici (interi e float)
- `string` — singolo o doppio apice
- `BigInt` — numeri > 2⁵³-1, suffisso `n` (es. `9007199254740993n`)

### Tipi speciali
- `any` — nessuna protezione dal compilatore
- `unknown` — protezione massima, richiede *type narrowing* prima dell'uso
- `never` — tipo impossibile (funzione che lancia sempre un'eccezione)
- `undefined` — variabile dichiarata ma non assegnata
- `null` — assenza esplicita di valore (scelta intenzionale)

## Tipizzazione e type inference

```typescript
let a;              // tipo any (nessuna annotazione)
let b: number;      // tipo number esplicito
let username = "alice";   // TS inferisce string
let score = 100;          // TS inferisce number
let flags = [true, false]; // TS inferisce boolean[]

// Casting
username as string;
```

La **type inference** funziona anche per oggetti e array. Il casting si fa con `as <tipo>`.

## Array

```typescript
const names: string[] = [];
names.push("Dylan");       // OK
// names.push(3);          // Error: type 'number'

const ro_names: readonly string[] = ["Dylan"];
// ro_names.push("Jack");  // Error: readonly
```

`readonly` impedisce modifiche successive all'array.

## Tuple

Una **tupla** è un array di lunghezza pre-definita che può contenere valori di tipo diverso. L'ordine è importante.

```typescript
let ourTuple: [number, boolean, string];
ourTuple = [5, false, 'hello'];   // OK
// ourTuple = [false, 'hello', 5]; // Error: ordine sbagliato

const readonlyTuple: readonly [number, boolean, string] = [5, true, 'hi'];
// readonlyTuple.push('x');  // Error: readonly

// Destructuring
const graph: [number, number] = [55.2, 41.3];
const [x, y] = graph;
```

Attenzione: `push()` su una tupla non-readonly funziona a runtime ma TS considera l'elemento extra fuori tipo.

## Object

```typescript
const car: { type: string, model: string, year: number } = {
  type: "Toyota", model: "Corolla", year: 2009
};

// Proprietà opzionale con ?
const right_car: { type: string, mileage?: number } = { type: "Toyota" };

// Index signature (mappa)
const nameAgeMap: { [index: string]: number } = {};
nameAgeMap.Jack = 25;  // OK
// nameAgeMap.Mark = "Fifty";  // Error: string non assegnabile a number
```

Type inference funziona anche per gli oggetti. Le proprietà opzionali si dichiarano con `?`.

## Enum

```typescript
enum CardinalDirections {
  North = 1,  // default sarebbe 0
  East,       // 2
  South,      // 3
  West        // 4
}
console.log(CardinalDirections.North); // 1

enum StringCardinalDirections {
  North = 'North',
  East = "East",
  South = "South",
  West = "West"
}
```

Un **enum** rappresenta un gruppo di costanti con nome. Può avere valori numerici (auto-incrementanti) o stringhe.

## Classi

```typescript
class Car {
  engine: string;  // public by default
  readonly id: number;

  constructor(engine: string) {
    this.engine = engine;
  }
}
var hondaAccord = new Car("V6");
```

Modificatori di visibilità: `public` (default), `private`, `protected`. `readonly` sulle proprietà di istanza impedisce la modifica dopo la costruzione.

### Ereditarietà

```typescript
class Truck extends Car {
  bigTires: boolean;
  constructor(engine: string, bigTires: boolean) {
    super(engine);  // chiama costruttore della classe base
    this.bigTires = bigTires;
  }
}
```

`extends` permette l'accesso ai membri pubblici della classe base. Il costruttore può essere ridefinito; `super()` chiama quello della classe padre.

### Classi astratte

```typescript
export abstract class DomainError extends Error {
  abstract kind: 'INVALID_INPUT' | 'NOT_FOUND';
  protected constructor(message: string) {
    super(message);
    this.name = this.constructor.name;
  }
}
// Non si può fare: new DomainError()
// Si può fare: new InvalidInputError("...")
```

## Interfacce

Le interfacce definiscono tipi complessi come contratti; le classi le implementano con `implements`:

```typescript
interface ICar {
  engine: string;
  color: string;
}

class Car implements ICar {
  engine: string;
  color: string;
  constructor(engine: string, color: string) {
    this.engine = engine;
    this.color = color;
  }
}
```

## Union types e type alias

```typescript
// Type alias semplice
export type ItemId = string;

// Union type discriminata (discriminated union)
export type Result<T, E> =
  | { ok: true; value: T }
  | { ok: false; error: E };

export type PaymentMethod =
  | { kind: 'CARD'; last4: string }
  | { kind: 'PAYPAL'; email: string };
```

I **discriminated union** usano una proprietà discriminante (`ok`, `kind`) per permettere il *narrowing*: TypeScript sa quali campi sono accessibili in ogni branch.

## Funzioni e arrow functions

```typescript
// Funzione tradizionale
var myFunc1 = function(h: number, w: number) {
  return h * w;
};

// Arrow function (equivalente)
var myFunc2 = (h: number, w: number) => h * w;

// Equivalenze
(x) => { return Math.sin(x); }
(x) => Math.sin(x)
x => Math.sin(x)
```

Le **arrow functions** risolvono il problema dello scope di `this`: il `this` non cambia (resta quello del punto in cui la funzione è definita). Con `function` tradizionale, `this` dipende da chi chiama (si «perde» nelle callback).

## Moduli

Un modulo è un file che:
- **esporta** ciò che vuole rendere pubblico (`export`)
- **importa** ciò che gli serve da altri moduli (`import`)
- crea un proprio scope senza inquinare quello globale

In Nx, TypeScript è la «colla» tra apps e libs: le libs si importano tramite alias configurati in `tsconfig.base.json`.

## Processo di compilazione

Il compilatore TypeScript (`tsc`) prende file `.ts` e produce file `.js`. Durante la compilazione:
1. **Type-check**: controlla la correttezza dei tipi (errori prima dell'esecuzione)
2. **Rimozione dei tipi**: annotazioni tipo, `interface`, `type` spariscono (non esistono in JS)
3. **Trasformazioni di sintassi**: adeguamento alla versione JS target specificata in `tsconfig`

### Compilatori in Nx

| Compiler | Caratteristica | Quando usare |
|---|---|---|
| **TSC** | type-check approfondito, più lento | fase di sviluppo iniziale |
| **SWC** | molto veloce, senza type-check | compilazioni frequenti |

```bash
# Build con TSC (default)
npx nx build <progetto>
# Build con SWC (target aggiuntivo in package.json)
npx nx run <progetto>:build-swc
```

## Articoli correlati

- [[introduzione-al-corso]]
- [[nx]]
- [[nodejs-nestjs]]
- [[reactjs]]
- [[typeorm]]

## Fonti

- `raw/L3a_TypeScript.pdf`
- `raw/L3b_Lab_TypeScript.pdf`
