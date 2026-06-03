---
tags: [web, full-stack, build-system, monorepo, tooling]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L2_Nx_gettingStarted.pdf
---

# Nx – Build System e Task Runner per Monorepo

Nx è un build system e task runner progettato per workspace multi-progetto (tipicamente *monorepo*). Automatizza build, test e lint, gestisce le dipendenze tra progetti e ottimizza le esecuzioni tramite caching e analisi delle modifiche.

## Punti chiave

- **Monorepo**: più progetti (frontend, backend, librerie condivise) nello stesso repository
- **Caching**: Nx calcola un *computation hash* dagli input; se coincide, ripristina output e log senza rieseguire
- **Affected**: esegue i target solo sui progetti impattati dalle modifiche recenti → riduce i task da lanciare
- **Project graph**: mappa visuale delle dipendenze tra progetti (`npx nx graph`)
- **Plugin system**: estende Nx con generator ed executor per tool specifici (NestJS, React, ecc.)
- Particolarmente utile con backend NestJS + frontend React + shared libs TypeScript nello stesso repo

## Glossario operativo

| Oggetto | Descrizione |
|---|---|
| **Workspace** | Repo Nx completo |
| **Project** | Singola app o libreria (ha nome e root) |
| **Task** | Azione eseguibile: build, test, lint, … |
| **Executor** | Come si esegue un task (implementazione) |
| **Generator** | Crea progetti o file standard (scaffolding) |
| **Target** | Cosa è dichiarato nel progetto (es. `ui:build`) |

## Struttura del workspace

```
workspace-root/
├── nx.json              # config Nx globale
├── package.json         # deps + script
├── tsconfig.base.json   # base TypeScript
├── .nx/                 # cache e metadata
├── apps/                # applicazioni
└── libs/                # librerie condivise
```

## File di configurazione chiave

### `nx.json` (workspace)
- **namedInputs**: quali file contano come input di un task (per caching)
- **plugins**: elenco dei plugin Nx attivi (tool, file di config, target disponibili)
- **generators**: opzioni di default per i generator (es. CSS, eslint, vite)
- **targetDefaults**: dipendenze tra task

### `package.json` (per progetto)
- **targets**: cosa è eseguibile sul progetto
  - `executor`: implementazione del target (o `nx:run-commands`)
  - `outputs`: cosa va in cache/restore
  - `configurations`: varianti per ambiente (prod/dev)

## Setup e comandi principali

### Creare il workspace
```bash
npx create-nx-workspace@latest
# oppure: pnpx / yarn dlx create-nx-workspace@latest
```
Viene chiesto: nome workspace, template (minimale / React / Angular / custom), se usare Nx Cloud.

### Aggiungere plugin e progetti
```bash
# Plugin NestJS
npx nx add @nx/nest
npx nx g @nx/nest:app apps/<nome-app>

# Plugin React
npx nx add @nx/react
npx nx g @nx/react:app apps/<nome-app>

# Visualizzare plugin disponibili
npx nx list
```

### Orientarsi nel workspace
```bash
npx nx show projects              # lista tutti i progetti
npx nx show project <nome>        # target disponibili del progetto
npx nx graph                      # visualizza grafo dipendenze
```

### Eseguire task
```bash
npx nx run <progetto>:<target>    # es. npx nx run my-app:lint
npx nx serve <app_name>           # avvia l'app (porta :3000 per NestJS, :4200 per React)

# Multi-progetto
npx nx run-many -t <target>       # esegue il target su tutti i progetti
npx nx affected -t <target>       # solo i progetti impattati dalle modifiche
```

### Caching
```bash
npx nx <target> --skip-nx-cache   # bypassa la cache
npx nx reset                      # reset cache + metadata (troubleshooting)
```

Il meccanismo di caching funziona così: `Input (file, config, env, args) → Hash → Cache hit? → restore output e log`.

## Quando usare Nx

- Progetti con più frontend/backend/librerie condivise
- Standardizzare script build/test/lint su tutto il team
- Ridurre tempi di CI/CD con cache + affected
- Imporre confini architetturali tra moduli/librerie

## Articoli correlati

- [[introduzione-al-corso]]
- [[nodejs-nestjs]]
- [[reactjs]]
- [[typescript]]

## Fonti

- `raw/L2_Nx_gettingStarted.pdf`
