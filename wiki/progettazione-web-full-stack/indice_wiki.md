---
tags: [web, full-stack, indice]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
---

# Wiki – Progettazione di Applicazioni Web Full Stack

Corso UniBS (Prof. Devis Bianchini) che copre lo sviluppo full-stack di applicazioni web data-intensive con TypeScript, Node.js/NestJS (backend), React (frontend), Nx (build system) e la progettazione model-driven con WebML.

Lo stack completo: `TypeScript + Nx (monorepo) → NestJS + TypeORM + PostgreSQL (backend) → React (frontend)`.

## Articoli

- [[introduzione-al-corso]] — Panoramica del corso: storia del web, stack tecnologico, REST/GraphQL/tRPC, SPA/SSR, WebML. Strumenti e modalità d'esame.
- [[nx]] — Build system e task runner per monorepo: workspace, plugin, caching, affected, comandi principali. Setup con NestJS e React.
- [[typescript]] — Superset tipizzato di JavaScript: tipi primitivi, array, tuple, oggetti, enum, classi, interfacce, union types, funzioni, moduli. Compilatori TSC e SWC.
- [[nodejs-nestjs]] — Framework backend opinionated: moduli, controller, provider, dependency injection, decoratori, routing, pipes, DTO, HTTP exceptions. Testing, Swagger, CJS/ESM.
- [[typeorm]] — ORM per PostgreSQL/MySQL: entity, decoratori colonne e relazioni (1:1, 1:N, M:N), repository pattern, DatabaseModule, struttura libreria-per-tabella in Nx.
- [[nestjs-auth]] — Autenticazione JWT con Passport: strategia local (bcrypt), strategia JWT, guard, RolesGuard, decorator `@Roles`, `@CurrentUser`, security module, Swagger Bearer Auth.
- [[reactjs]] — Libreria frontend SPA: componenti, JSX, props, state (useState), useEffect, useContext, react-router-dom, eventi, stile CSS (inline/modules/Tailwind/Bootstrap), Virtual DOM, CORS, API layer.
- [[webml]] — Metodologia model-driven per app web data-intensive: requisiti, dizionario dati, matrice gruppi-oggetti, progettazione dati (core/accesso/interconnessione/personalizzazione), progettazione ipertesto (coarse + dettagliato), unità di contenuto, link, corrispondenza React.
