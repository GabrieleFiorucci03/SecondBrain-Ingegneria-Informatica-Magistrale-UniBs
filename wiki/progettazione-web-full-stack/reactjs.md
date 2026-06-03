---
tags: [web, full-stack, frontend, react, jsx, componenti, stato, hooks]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L8_RectJS.pdf
  - raw/L8b_Lab_RectJS.pdf
  - raw/L8c_Lab_RectJS_approfondimenti.pdf
---

# React.js – Sviluppo Frontend

React.js è una libreria JavaScript/TypeScript per lo sviluppo frontend di applicazioni web basate sul paradigma **SPA** (Single Page Application). Sviluppata da Facebook (2011), open source dal 2013, è usata da AirBnB, Netflix, PayPal, Uber. In Nx si accoppia con NestJS per formare lo stack full-stack completo.

## Punti chiave

- **Componenti**: unità fondamentale — un «pezzo» di UI con logica + aspetto (HTML o componenti annidati)
- **JSX**: sintassi ibrida HTML-in-JavaScript; i componenti si scrivono come `<MyComponent prop="val" />`
- **Props**: input del componente (immutabili dall'interno); **State**: stato interno mutabile con `useState`
- **`useEffect`**: per operazioni con side-effect (fetch dati, sottoscrizioni) dopo il render
- **`useContext`**: condivisione di stato globale senza prop drilling (es. utente autenticato, tema)
- **`react-router-dom`**: navigazione client-side senza ricarico pagina (`BrowserRouter`, `Routes`, `Route`, `Link`, `useNavigate`)
- In Nx la app React si crea con `npx nx g @nx/react:app apps/<nome>` — porta :4200 di default

## Struttura base di un'app React in Nx

```
apps/ui/
├── index.html           # entry point HTML — contiene <div id="root">
└── src/
    ├── main.tsx         # bootstrapping React
    └── app/
        ├── app.tsx      # componente radice, definisce le Routes
        └── ...
```

### `index.html`
```html
<div id="root"></div>
<script type="module" src="/src/main.tsx"></script>
```

### `main.tsx` — bootstrapping
```tsx
import { StrictMode } from 'react';
import { BrowserRouter } from 'react-router-dom';
import * as ReactDOM from 'react-dom/client';
import App from './app/app';

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
root.render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>
);
```

| Elemento | Ruolo |
|---|---|
| `ReactDOM.createRoot` | API moderna React 18 — crea root React nel `<div id="root">` |
| `StrictMode` | Attiva controlli extra in sviluppo; non aggiunge UI visibile |
| `BrowserRouter` | Abilita routing client-side; tutti i figli possono usare `Routes`, `Route`, `Link`, `useNavigate` |

## JSX (JavaScript XML)

JSX = HTML dentro JavaScript. Permette di scrivere markup nel codice senza usare `createElement` / `appendChild`.

**Regole chiave:**
- Componenti → CamelCase: `<MyComponent />`
- HTML native → minuscolo: `<div>`, `<input />`
- Ogni tag aperto deve avere un tag di chiusura (o essere self-closing: `<img />`)
- Espressioni JS dentro JSX: `{variabile}`, `{funzione()}`, `{condizione ? a : b}`
- Attributi CSS inline: `style={{ color: 'red' }}` (doppio `{{`: outer = JSX, inner = oggetto JS)

## Componenti funzionali

Un componente funzionale è una **funzione TypeScript** che riceve props e ritorna JSX:

```tsx
// Dichiarazione con destructuring delle props
export function NxWelcome({ title }: { title: string }) {
  return (
    <div>
      <h1>Welcome {title}</h1>
    </div>
  );
}
export default NxWelcome;

// Utilizzo
<NxWelcome title="@org/ui" />
```

- `export default` → import senza graffe: `import NxWelcome from './nx-welcome'`
- Named export → import con graffe: `import { NxWelcome } from './nx-welcome'`

## Routing con react-router-dom

```tsx
// app.tsx
import { Routes, Route, Link } from 'react-router-dom';

export function App() {
  return (
    <div>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <Routes>
        <Route path="/" element={<HomePage />} />
        <Route path="/about" element={<AboutPage />} />
        <Route path="/users/:id" element={<UserDetail />} />
      </Routes>
    </div>
  );
}
```

- Tutto fuori da `<Routes>` (es. la navbar) è fisso — non cambia con la navigazione
- `<Link>` sostituisce `<a>` per navigazione senza ricarico pagina
- `useNavigate()` per navigazione programmatica: `const navigate = useNavigate(); navigate('/login')`
- `useParams()` per leggere parametri URL: `const { id } = useParams()`

## Rendering condizionale e liste

```tsx
// Condizionale
function Panel({ isLoggedIn }: { isLoggedIn: boolean }) {
  let content;
  if (isLoggedIn) content = <AdminPanel />;
  else content = <LoginForm />;
  return <div>{content}</div>;
}

// Inline con operatore ternario
<div>{isLoggedIn ? <AdminPanel /> : <LoginForm />}</div>
// Short-circuit (solo se true)
<div>{isLoggedIn && <AdminPanel />}</div>

// Lista con map()
const products = [{ id: 1, title: 'Cabbage', isFruit: false }, ...];
const listItems = products.map(p =>
  <li key={p.id} style={{ color: p.isFruit ? 'magenta' : 'darkgreen' }}>
    {p.title}
  </li>
);
return <ul>{listItems}</ul>;
```

`key` è obbligatoria nelle liste per permettere a React di ottimizzare il re-render.

## Eventi

```tsx
function MyButton() {
  function handleClick() { alert('You clicked me!'); }
  return <button onClick={handleClick}>Click me</button>;
}
```

Handler passato come riferimento (`{handleClick}` non `{handleClick()}`). Eventi supportati: `onClick`, `onDoubleClick`, `onChange`, `onSubmit`, `onKeyDown`, ecc.

**Condivisione dello stato e sollevamento (lifting state up):**
Quando due componenti devono condividere lo stesso stato, lo stato va spostato nel genitore comune e passato verso il basso tramite props:

```tsx
// Genitore gestisce lo stato
function MyApp() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <MyButton count={count} onClick={() => setCount(count + 1)} />
      <MyButton count={count} onClick={() => setCount(count + 1)} />
    </div>
  );
}
// Figlio riceve tutto tramite props
function MyButton({ count, onClick }: { count: number; onClick: () => void }) {
  return <button onClick={onClick}>Clicked {count} times</button>;
}
```

## Stile CSS

| Approccio | Sintassi | Pro | Contro |
|---|---|---|---|
| **Inline** | `style={{ fontSize: '3rem', fontWeight: 500 }}` | Veloce, locale | Poco leggibile; proprietà in camelCase; valori in stringhe |
| **CSS file** | `import './component.css'`; `<div className="welcome">` | Standard, familiare | Conflitti di nomi globali |
| **CSS modules** | `import styles from './component.module.css'`; `<div className={styles.welcome}>` | Nessun conflitto | Più verboso |
| **Globale** | `<link>` in `index.html` | Condiviso ovunque | Tutto globale |

Note importanti:
- In JSX si usa `className` invece di `class` (parola riservata JS)
- Stile inline: oggetto JS → proprietà in camelCase (`font-weight` → `fontWeight`), valori in string
- `dangerouslySetInnerHTML={{ __html: '...' }}` inietta HTML raw — **rischio XSS**, usare solo con contenuto trusted

## Virtual DOM

React non manipola direttamente il DOM del browser ad ogni evento. Invece:

1. Ogni evento aggiorna il **Virtual DOM** (copia in memoria del DOM reale)
2. React confronta il nuovo Virtual DOM con quello precedente (**diffing**)
3. Propaga al browser **solo le modifiche strettamente necessarie** → *rendering selettivo*

**Vantaggi:**
- Per lo sviluppatore: componenti riusabili, composizione di «mattoncini» — React gestisce tutto il DOM
- Per l'utente: rendering selettivo = UI più veloce e reattiva

## State con `useState`

```tsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);  // [valore, setter]

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
    </div>
  );
}
```

- Lo stato è **locale** al componente — ogni istanza ha il suo
- Chiamare il setter aggiorna il valore e **re-renderizza** il componente
- Per stato complesso usare l'overload con funzione updater: `setCount(prev => prev + 1)`

## `useEffect` – Side effects

```tsx
import { useEffect, useState } from 'react';

function UserList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch('/api/users')
      .then(r => r.json())
      .then(data => setUsers(data));
  }, []);  // [] = esegui solo al mount

  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

- **Secondo argomento** (dependency array): `[]` → solo al mount; `[dep]` → ogni volta che `dep` cambia; omesso → ogni render
- Può ritornare una **cleanup function** (es. clearInterval, unsubscribe)

## `useContext` – Stato globale

```tsx
// auth-context.tsx
export const AuthContext = createContext<AuthenticatedUser | null>(null);

// Provider (alto nell'albero)
<AuthContext.Provider value={currentUser}>
  <App />
</AuthContext.Provider>

// Consumer (qualsiasi discendente)
const user = useContext(AuthContext);
```

`useContext` evita il *prop drilling* (passare props attraverso molti livelli). Ideale per: utente autenticato, tema, lingua.

## Custom hooks

Funzioni che iniziano con `use` e possono chiamare altri hook:

```tsx
function useAuth() {
  const [user, setUser] = useState<AuthenticatedUser | null>(null);

  const login = async (email: string, password: string) => {
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      body: JSON.stringify({ email, password }),
      headers: { 'Content-Type': 'application/json' }
    });
    const data = await response.json();
    setUser(data.user);
    localStorage.setItem('token', data.access_token);
  };

  return { user, login };
}
```

## Setup in Nx

```bash
# Plugin e app
npx nx add @nx/react
npx nx g @nx/react:app apps/<nome>

# Avviare il frontend
npx nx serve <nome-app>    # porta 4200 di default
```

### Proxy per il backend (Vite)
Il frontend gira su :4200, il backend su :3000. Per evitare CORS in sviluppo, configurare il proxy in `vite.config.ts`:

```typescript
export default defineConfig({
  server: {
    proxy: {
      '/api': { target: 'http://localhost:3000', changeOrigin: true }
    }
  }
});
```

## CORS – Comunicazione frontend/backend

Quando React (:4200) chiama NestJS (:3333) i browser bloccano la richiesta per la *Same-Origin Policy*. Si abilita CORS nel `main.ts` del backend:

```typescript
app.enableCors({
  origin: 'http://localhost:4200',
  credentials: true,
  allowedHeaders: ['Content-Type', 'Authorization'],
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS']
});
```

In alternativa si può usare il proxy Vite (vedi sezione Setup).

## Architettura consigliata: API layer

Separare le chiamate HTTP in file dedicati:

```typescript
// auth.api.ts
const API_URL = 'http://localhost:3333/api';

export async function login(email: string, password: string) {
  const response = await fetch(`${API_URL}/auth/login`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password }),
  });
  if (!response.ok) throw new Error('Credenziali non valide');
  const data = await response.json();
  localStorage.setItem('access_token', data.access_token);
  return data;
}

// books.api.ts
export async function fetchBooks(): Promise<BookListItem[]> {
  const token = localStorage.getItem('access_token');
  const response = await fetch(`${API_URL}/books`, {
    headers: { Authorization: `Bearer ${token}` }
  });
  if (!response.ok) throw new Error('Errore durante il caricamento dei libri');
  return response.json();
}
```

Il token JWT viene salvato in `localStorage` e allegato all'header `Authorization: Bearer` nelle richieste successive.

## Pattern pagina con login

```tsx
export function LoginPage() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState<string | null>(null);
  const [loading, setLoading] = useState(false);
  const navigate = useNavigate();

  async function handleSubmit(e: React.FormEvent) {
    e.preventDefault();
    setError(null);
    setLoading(true);
    try {
      await login(email, password);
      navigate('/books');
    } catch (err: any) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }

  return (
    <main>
      <form onSubmit={handleSubmit}>
        <input value={email} onChange={(e) => setEmail(e.target.value)} required />
        <input type="password" value={password} onChange={(e) => setPassword(e.target.value)} required />
        <button type="submit" disabled={loading}>
          {loading ? 'Accesso in corso...' : 'Login'}
        </button>
      </form>
      {error && <p>{error}</p>}
    </main>
  );
}
```

Pattern chiave: `loading` disabilita il pulsante durante la richiesta; `finally` riabilita sempre il pulsante.

## Pattern pagina lista dati

```tsx
export function BooksPage() {
  const [books, setBooks] = useState<BookListItem[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetchBooks()
      .then(setBooks)
      .catch((err) => setError(err.message))
      .finally(() => setLoading(false));
  }, []);  // eseguito solo al mount

  if (loading) return <p>Caricamento...</p>;
  if (error) return <p>Errore: {error}</p>;

  return (
    <table>
      <tbody>
        {books.map((book) => (
          <tr key={book.id}>
            <td>{book.title}</td>
            <td>{book.publishedYear}</td>
            <td>{book.category?.name ?? 'N/D'}</td>
            <td>{book.authors?.length ? book.authors.map(a => `${a.firstName} ${a.lastName}`).join(', ') : 'N/D'}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

`??` (nullish coalescing): fallback se il valore è `null` o `undefined`. `?.` (optional chaining): accesso sicuro a proprietà potenzialmente undefined.

## Framework CSS

### Tailwind (utility-first)
Ogni classe CSS corrisponde direttamente a una proprietà CSS. Nessun file CSS separato, nessun naming, design system consistente.

```bash
npm install -D tailwindcss@3 postcss autoprefixer
```

Configurazione file richiesti in Nx + React + Vite:
- `apps/ui/postcss.config.cjs`: plugin Tailwind e autoprefixer
- `apps/ui/tailwind.config.cjs`: content paths `['./src/**/*.{html,js,ts,jsx,tsx}']`
- `apps/ui/src/styles.css`: direttive `@tailwind base; @tailwind components; @tailwind utilities;` (importato in `main.tsx`)

```tsx
// Stesso risultato di .card { bg:white; padding:2rem; border-radius:12px }
<div className="bg-white p-8 rounded-xl">
```

### Bootstrap (component-first)
Fornisce componenti già pronti. Più semplice da iniziare, meno flessibile di Tailwind.

```bash
npm install bootstrap
# In main.tsx:
import 'bootstrap/dist/css/bootstrap.min.css';
```

```tsx
<section className="card shadow p-5 text-center">
  <Link to="/books" className="btn btn-primary mt-4">Vai al catalogo</Link>
</section>
```

### Confronto

| | CSS modules | Tailwind | Bootstrap |
|---|---|---|---|
| Approach | CSS separato per componente | utility classes inline | componenti pre-fatti |
| Conflitti | Nessuno | Nessuno | Possibili |
| Flessibilità | Massima | Alta | Media |
| Velocità setup | Semplice | Media | Semplice |
| Ideale per | controllo totale | design system custom | prototipazione rapida |

**`clsx`**: utility per combinare più classi CSS condizionalmente.
```tsx
import clsx from 'clsx';
<div className={clsx(styles.card, styles.cardLarge)} />  // unisce più classi CSS module
```

## Articoli correlati

- [[typescript]]
- [[nodejs-nestjs]]
- [[nx]]
- [[nestjs-auth]]
- [[webml]]

## Fonti

- `raw/L8_RectJS.pdf`
- `raw/L8b_Lab_RectJS.pdf`
- `raw/L8c_Lab_RectJS_approfondimenti.pdf`
