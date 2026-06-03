---
tags: [web, full-stack, architetture, storia-web, api, spa, ssr]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L1_Introduzione_al_corso.pdf
---

# Introduzione al Corso – Progettazione Web Full Stack

Corso UniBS (Prof. Devis Bianchini, a.a. 2025) che copre architetture web moderne, sviluppo full-stack con TypeScript/Node.js/React e progettazione model-driven con WebML. L'approccio è sia pratico (laboratori) sia metodologico (design pattern e modellazione).

## Punti chiave

- **Stack principale**: [[typescript]] + [[nodejs-nestjs]] (backend) + [[reactjs]] (frontend) + [[nx]] (build system)
- **Tre pilastri** dell'evoluzione web: *Runtime* (dove gira il codice), *API & Dati* (come esponiamo servizi), *UI & Stato* (come costruiamo interfacce)
- Le grandi «svolte» tecnologiche arrivano quando cambia uno di questi tre pilastri
- **Esame**: prova scritta al calcolatore + elaborato progettuale (concordato con docente) + orale
- TypeScript è il «collante» dello stack: usato sia su Node (backend) che React (frontend)
- La progettazione model-driven (WebML) permette di separare dati, ipertesto e presentazione

## Storia dello sviluppo web

### Preistoria e fondamenta (1965–1990)
- **1965** – Comunicazione a pacchetti (MIT Lincoln Lab)
- **1969** – ARPANET, predecessore di Internet
- **1983** – TCP/IP → nasce Internet («rete di reti»)
- **1988** – IRC (Jarkko Oikarinen): prime app per collegare persone

### Dal documento all'applicazione
- **Web statico**: pagine HTML, navigazione ipertestuale
- **Dinamicità server-side**: CGI/PHP generano HTML on-demand — limite UX: ogni interazione richiede un *postback* (ricarico pagina)
- **DOM + JavaScript**: il browser diventa piattaforma programmabile; manipolazione runtime della pagina senza ricaricare
- **Guerra dei browser**: frammentazione → motori JS sempre più veloci → **V8** (Google, 2008): JIT, inline caching → JS efficiente fuori dal browser

### Node.js e l'ecosistema npm
- **Node.js = V8 + runtime server-side**: stesso linguaggio su client e server
- **npm**: package manager con >2,5M pacchetti (di gran lunga il registry più grande); rovescio: dipendenze profonde, typosquatting, vulnerabilità
- Alternative: yarn, pnpm

### TypeScript
- JS «scala male» con la complessità (bug silenziosi, refactor rischiosi)
- TypeScript aggiunge un sistema di tipi a compile-time; in produzione gira comunque JS
- Benefici: errori precoci, autocomplete, refactor sicuri

### NestJS
- Node.js puro porta a «codice spaghetti» nei progetti grandi
- **NestJS**: framework backend opinionated per Node, scritto in TypeScript
- Struttura: *modules* (composizione), *controllers* (routing/API), *services* (logica di dominio)
- Integra nativamente REST, auth, validation, ORM, microservizi

## API: REST, GraphQL, tRPC

| | REST | GraphQL | tRPC |
|---|---|---|---|
| Modello | risorse + HTTP (GET/POST/PUT/DELETE) | schema + query client-driven | RPC tipizzato end-to-end |
| Punti forti | standard, API pubbliche | riduce over/under-fetching | typesafety totale con TS |
| Ideale per | API pubbliche, servizi generici | UI complesse, aggregazione dati | monorepo TS-first, team piccoli |

- **REST**: stateless, separazione client/server, scalabilità orizzontale; TypeScript utile per definire i DTO (Data Transfer Object)
- **GraphQL**: il client chiede esattamente i campi necessari → meno round-trip; richiede schema, resolver e caching più complessi
- **tRPC**: TypeScript come «contratto vivo» tra backend e frontend; il client inferisce i tipi direttamente dal backend

## Frontend: AJAX → SPA → SSR → Transitional

- **AJAX** (Asynchronous JavaScript And XML): aggiornamento parziale UI senza ricarico pagina → nascono le **SPA** (Single Page Application)
- **SPA**: l'app gira nel browser, gestisce routing, stato, chiamate REST/GraphQL — prezzo: state management, bundle size, sincronizzazione dati
- **React**: UI come funzione dello stato, componenti riusabili, approccio dichiarativo, Virtual DOM
- **SSR** (Server-Side Rendering): il server genera HTML già renderizzato → contenuto subito visibile, ma non ancora interattivo → serve *hydration* (scarica bundle JS, collega event handler, ricostruisce lo stato)
- **App transitional**: combina più strategie per pagina/sezione (es. e-commerce: Home=SSR, Prodotto=SSR+isole interattive, Admin=SPA)

## Progettazione model-driven

Un sito web ha tre livelli ortogonali:
- **Dati**: quali informazioni, organizzazione logica → modelli: ER, relazionale, UML
- **Ipertesto**: come i dati sono organizzati in pagine e link navigazionali → **[[webml]]** (Web Modeling Language)
- **Presentazione**: l'aspetto visivo → HTML/CSS, XML

Possiamo cambiare ipertesto e presentazione senza toccare i dati. Per *data-intensive web sites* (grandi quantità di dati, struttura ipertestuale significativa) ha senso applicare schemi e modelli anche al livello ipertestuale.

## Materiale del corso

- Frank Zammetti, *Modern Full-Stack Development* (2022, Apress)
- Andrew Mead, *Learning Node.js Development* (2018, Packt)
- Alex Banks, Eve Porcello, *Learning React* (2020, O'Reilly)
- Christopher D. Hundhausen, *Full-stack Web Development from the Ground Up* (2026, Morgan Kaufmann)

## Articoli correlati

- [[nx]]
- [[typescript]]
- [[nodejs-nestjs]]
- [[reactjs]]
- [[webml]]
- [[typeorm]]
- [[nestjs-auth]]

## Fonti

- `raw/L1_Introduzione_al_corso.pdf`
