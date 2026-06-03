---
tags: [web, full-stack, metodologia, model-driven, progettazione, webml]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L7_WebML_progettazioneDati.pdf
  - raw/L9_WebML_progettazioneIpertesto.pdf
  - raw/L10_Lab_WebML_progettazioneIpertesto.pdf
---

# WebML – Progettazione Model-Driven di Applicazioni Web

WebML (Web Modeling Language) è una metodologia di sviluppo per applicazioni web *data-intensive* che guida la progettazione dei contenuti informativi e dell'ipertesto, traducendosi poi in logica di backend (NestJS/TypeORM) e frontend (React). Evoluzione rispetto alla Guida di Yale: propone un linguaggio formale per modellare insieme dati e struttura ipertestuale.

## Punti chiave

- **Tre livelli ortogonali**: Dati (cosa), Ipertesto (come è organizzato), Presentazione (come appare) — separati e modificabili indipendentemente
- **Processo**: Requisiti → Progettazione dati → Progettazione ipertesto → Implementazione → Promozione/monitoraggio
- **Matrice gruppi-oggetti**: strumento chiave per verificare la consistenza dei permessi prima di codificare le guard
- La matrice anticipa i **bug di progettazione**: oggetti leggibili da nessuno, oggetti scrivibili da nessuno, ruoli senza responsabilità
- **Dizionario dei dati**: il ponte tra requisiti informali e il modello ER/UML; va prodotto prima del data model
- I mockup (freddi/tiepidi) supportano la raccolta e analisi dei requisiti — non sono wireframe estetici ma strumenti di comunicazione con il cliente

## La Guida di Yale (contesto storico)

Riferimento classico (Lynch & Horton, Yale University Press, 1999) per siti web chiari, efficaci e usabili. Copriva: strategia, pianificazione, interfaccia, contenuti, usabilità, accessibilità, manutenzione. Limite: non affrontava la modellazione formale di dati e ipertesto per applicazioni data-intensive.

## La metodologia WebML – fasi

```
1. Specifica dei requisiti
   ├── Raccolta requisiti (utenti, funzionalità, dati, mockup)
   └── Analisi requisiti (UML use-case, dizionario, matrice)
2. Progettazione dei dati (→ ER/UML class diagram)
3. Progettazione dell'ipertesto (→ WebML hypertext model)
4. Implementazione (NestJS + TypeORM + React)
5. Promozione, monitoraggio, valutazione, manutenzione
```

## Fase 1: Specifica dei requisiti

### Raccolta
Attività poco strutturata, produce:
- **Utenti e gruppi utente** (es. visitatore, registrato, admin)
- **Requisiti funzionali** (casi d'uso: CRUD News, Modifica profilo, ...)
- **Requisiti sui dati** (dizionario: quali oggetti, proprietà, relazioni)
- **Mockup dell'interfaccia** (prototipo freddo o tiepido)
- **Requisiti non funzionali** (usabilità, sicurezza, scalabilità, ...)

### Analisi
Revisione e formalizzazione dei requisiti con:
- **UML use-case diagram** per gruppi utente e casi d'uso
- Soluzioni ad-hoc per dizionario dei dati e mockup

### Gerarchia dei gruppi utente
I gruppi utente si modellano con una gerarchia UML (ereditarietà):
- **Registrazione**: User → {Registrato, Non Registrato} → {Admin, Product Manager, Mar-Com Manager, Cliente Registrato, Fornitore}
- **Rete**: User → {Interno, Fornitore, Cliente} → ...
- Regola: **almeno un gruppo target** deve essere identificato

Ogni gruppo si specifica con una scheda: nome, descrizione, dati di profilo, super-gruppo, sotto-gruppi, oggetti in lettura/scrittura, casi d'uso.

### Dizionario dei dati
Elenco degli elementi informativi principali. Per ogni elemento:
- Nome e sinonimi
- Descrizione con istanze di esempio
- Proprietà (attributi)
- Relazioni con altri elementi (is-a: generalizzazione/specializzazione; part-of: composizione)

### Mockup dell'interfaccia
| Tipo | Descrizione |
|---|---|
| **Freddo** (low-fidelity) | Wireframe (es. Balsamiq): layout, contenuti, navigazione — non il design visivo |
| **Tiepido** (mid-fidelity) | Pagine HTML/CSS statiche con interazioni JS di base — valida comportamento e prime scelte visive |

### Matrice gruppi-oggetti (analisi dei requisiti)
La **mappatura** tra gruppi utente e oggetti/casi d'uso produce la matrice che permette di:
1. Definire le rotte dell'app (READ → GET, CREATE/UPDATE/DELETE → POST/PATCH/DELETE)
2. Configurare le **guard** NestJS in modo guidato
3. Identificare **bug di progettazione** prima di scrivere codice

```
Oggetti in lettura → azioni READ (GET)
Oggetti in scrittura + casi d'uso → CREATE/UPDATE/DELETE
```

**Esempio – Biblioteca online:**

| | Visitatore | Registrato | Bibliotecario | Admin |
|---|---|---|---|---|
| Book | R | (CUD)R | CRUD | |
| Author | R | (CUD)R | CRUD | |
| Address | R | (CUD)R | CRUD | |
| Category | R | R | CRUD | |
| Utente registrato | | (CRUD) | CRUD | CRUD |
| Utente bibliotecario | | | (CRUD) | CRUD |

**Errori frequenti da rilevare con la matrice:**
- Una colonna tutta vuota → ruolo inutile
- Una riga con solo R per tutti → nessuno crea/aggiorna quell'oggetto (chi gestisce gli indirizzi?)
- Il visitatore non vede nulla → sito «posizionato» interamente dietro autenticazione

## Fase 2: Progettazione dei dati

**Input**: dizionario dei dati  
**Output**: schema Entity-Relationship o class diagram UML

### Modello ER in WebML

WebML usa le primitive ER:
- **Entità**: classi di oggetti omogenei nel dominio
- **Attributi**: proprietà con valore semplice e atomico
- **Relazioni**: connessioni tra entità (con cardinalità)
- **Gerarchia IS-A**: classificazione/raggruppamento di entità

### Classificazione degli oggetti informativi

| Tipo | Descrizione |
|---|---|
| **Core** | Contenuti informativi principali gestiti dall'app (dal punto di vista dei gruppi utente target) |
| **Interconnessione** | Associazioni semantiche tra entità core, per link e indici navigazionali |
| **Accesso** | Oggetti ausiliari per classificare/specializzare gli oggetti core (categorie, lookup search) |
| **Personalizzazione** | Entità che descrivono proprietà degli utenti per adattare l'app a esigenze diverse |

### Sottoschema «core»

Un **sottoschema core** è un insieme di entità che collettivamente rappresentano un singolo concetto core. Procedura:
1. Il concetto core → **entità core**
2. Proprietà semplici e atomiche → **attributi** dell'entità core
3. Proprietà con valori multipli o strutturati → **componenti** interni (entità satellite)

**Cardinalità dei componenti interni:**
- **1:1 dalla parte del componente** → *entità debole* (part-of stretto): non può esistere senza l'entità core; non è condivisibile tra più istanze core → cancellazione core ⇒ cancellazione a cascata del componente
- **0:N dalla parte del componente** → *parte condivisa*: l'istanza può esistere indipendentemente, oppure essere condivisa tra entità core diverse → non è un'entità core a sé stante

**Implicazioni sulla progettazione dell'ipertesto:**
- Una pagina di dettaglio per ogni sottoschema core (per ogni utente target)
- La pagina di dettaglio di un componente include sempre le info sull'entità core padre
- Per le entità deboli, creazione/cancellazione è sempre contestuale all'entità core (fusione delle rotte)

### Sottoschema «interconnessione»

**Oggetti di interconnessione**: associazioni semantiche tra entità core, usate per definire link e indici tra pagine di dettaglio diverse.

- Derivano dalle associazioni tra oggetti core nel dizionario dei dati
- Ogni associazione semantica → **relazione M:N** nel modello ER (salvo vincoli specifici nel dizionario)

**Implicazioni sulla progettazione:**
- Crea un link dalla pagina di dettaglio dell'*entità sorgente* alla pagina di dettaglio dell'*entità destinazione*
- Implica una rotta che, dato l'id della sorgente, estrae i dettagli della destinazione

### Sottoschema «accesso»

**Oggetti di accesso**: entità ausiliarie per classificare o specializzare le entità core, facilitando l'accesso in modi diversi (categorie, ricerca, selezioni speciali).

**Tre tipi di categorizzazione:**
- **Gerarchica**: i concetti di categorizzazione formano a loro volta una gerarchia (Razza → Gruppo → Animale)
- **Multipla**: lo stesso concetto core è soggetto a più categorizzazioni indipendenti (Allevamento per Regione e per Tipologia)
- **Condivisa**: lo stesso oggetto di categorizzazione classifica più tipi di concetti core (Regione classifica sia Allevamento che Centro veterinario)

**Implicazioni sulla progettazione:**
- Elemento grafico nel frontend: componente per esplorare gerarchie di categorizzazione + form di lookup search
- Set di rotte che restituiscono istanze core filtrate per categoria o tramite lookup search

### Sottoschema «personalizzazione»

**Oggetti di personalizzazione**: entità e relazioni che descrivono proprietà degli utenti rilevanti per personalizzare l'app.
- **Dati di profilo utente**: sotto-classi di User nel modello ER
- **Relazioni di personalizzazione**: tra User e entità di accesso o core (es. PaginePiùVisitate, UltimoAcquisto)

Le relazioni di personalizzazione esprimono le preferenze degli utenti come:
- valori di attributi nell'entità Utente
- istanze di relazioni tra l'entità Utente e un'entità di accesso o core

**Implicazioni sulla progettazione:**
- L'id dell'utente deve essere incluso in tutte le rotte che estraggono istanze dalle entità core/accesso collegate all'utente tramite le relazioni di personalizzazione

La progettazione dei dati porta direttamente alle **TypeORM entity**:
- Ogni entità del modello → classe con `@Entity`
- Ogni attributo → `@Column`
- Ogni relazione → `@OneToOne`, `@OneToMany`, `@ManyToMany`
- La struttura del modello guida anche le API (rotte, DTO, validazione)

*Vedi: [[typeorm]] per i dettagli implementativi*

## Fase 3: Progettazione dell'ipertesto

**Input**: schema dei dati, mappa del sito, requisiti funzionali, requisiti utente  
**Output**: schema di ipertesto WebML

La progettazione risponde a quattro domande fondamentali:
1. In che modo l'utente fruisce del contenuto?
2. Quali sono le pagine (nodi dell'ipertesto)?
3. Quale informazione è pubblicata in ogni pagina?
4. Come i nodi sono collegati tra loro?

Due passi: **Progetto coarse** (preliminare) → **Progetto dettagliato**.  
Concetto chiave: la **site view** = «vista sul sito» per ciascun gruppo utente.

### Progetto coarse

Per ogni site view:
1. **Individuazione delle aree**: revisione requisiti funzionali + mappa del sito
2. **Definizione della visibilità delle aree**:
   - *Pubblica*: accesso consentito a tutti
   - *Privata*: accesso tramite autenticazione/autorizzazione
   - Ogni area può essere: *default*, *landmark*, *interna* (raggiungibile solo tramite link espliciti)
3. **Specifica dei contenuti tramite primitive**

#### Primitive del progetto coarse

| Primitiva | Sintassi | Significato |
|---|---|---|
| **Core** | `Core(CoreEntity, C1..CN)` | Pubblicazione contenuti di un'entità core e sue componenti |
| **Access** | `Access(CoreEntity, A1..AN)` | Accesso a istanze core tramite entità di categorizzazione |
| **Interconnection** | `Interconnection(Entity, Role1..N)` | Navigazione da istanze di Entity verso entità connesse tramite ruoli |
| **Personalization** | `Personalization(Entity, Role1..N)` | Contenuti filtrati dal profilo dell'utente autenticato |
| **Create** | `Create(Entity)` | Creazione istanza |
| **Create&Connect** | `Create&Connect(Entity, R1..N)` | Creazione + collegamento con entità correlate |
| **Modify** | `Modify(Entity)` | Modifica istanza |
| **Delete** | `Delete(Entity)` | Cancellazione istanza |

**Esempio – Area libri (Libreria online)**:
```
Area libri
  Core(Book)
  Create&Connect(Book, Book2Category, Book2Author)
  Modify(Book)
  Delete(Book)
```

### Progetto dettagliato

Per ogni area:
1. **Individuazione delle pagine**: suddivisione dell'area in pagine; ogni pagina include una porzione di contenuto/funzioni
2. **Visibilità di pagina**:
   - **HomePage** `[H]`: prima pagina visitata (primo accesso o dopo login)
   - **Pagina di Default**: pagina standard dell'area
   - **Pagina Landmark** `[L]`: accessibile da ogni altra pagina della site view (equivale a link non contestuali da tutte le altre pagine) → determina la **navbar**
   - **Pagina Interna**: raggiungibile solo tramite link espliciti
3. **Specifica di pagina**: usa sottoschemi di ipertesto e design pattern

**Esempio – Progetto dettagliato area libri**:
```
Catalogo libri [L]     → Core(Book) + Delete(Book)
Crea libro [L]         → Create&Connect(Book, Book2Category, Book2Author)
Modifica libro         → Modify(Book)
```

### Unità di contenuto (View Components)

Un'unità di contenuto corrisponde a una «vista» su un contenitore di oggetti — tutte le istanze di un'entità sorgente, o un sottoinsieme filtrato da un **selettore**.

```
unitX
Sorgente [Selettore]
```

**Tipi di unità di base:**

| Tipo | Icona | Descrizione |
|---|---|---|
| **Details** | profilo singolo | Mostra i dettagli di una singola istanza `[ID==1]` |
| **SimpleList** | lista | Lista di istanze senza dettagli `[pubY==2026]` |
| **MultipleDetails** | profili multipli | Lista di istanze con dettagli `[pubY==2026]` |
| **Form** | form | Raccoglie input dall'utente (create/modify) |
| **Scroller** | frecce | Naviga un'istanza per volta `[ID==1]` |
| **List** | lista con link | Lista con link a istanze correlate `[Autore2Libro]` |
| **Hierarchy** | albero | Naviga gerarchie di categorizzazione |

### Link di navigazione

- **Link non contestuali**: navigano da una pagina all'altra senza passare parametri (freccia rossa)
- **Link contestuali**: passano parametri di contesto (es. ID dell'istanza selezionata) — freccia tratteggiata
- **Landmark**: una pagina landmark è raggiungibile da ogni pagina della site view → corrisponde ai link della navbar

### Corrispondenza progetto → implementazione React

| Modello WebML | Implementazione |
|---|---|
| Pagina | Componente React + Route |
| View component (visualizzazione) | Componente React |
| View component (caricamento dati) | Funzione in `api.ts` |
| Operation (Create/Modify/Delete) | Funzione in `api.ts` |
| Link automatici (al caricamento) | `useEffect` |
| Pagine landmark / navbar | Componente React `<Navbar>` con i `<Link>` landmark |
| XOR (pagine alternative) | `{isLoggedIn ? <Page A> : <Page B>}` |

## Articoli correlati

- [[introduzione-al-corso]]
- [[typeorm]]
- [[nodejs-nestjs]]
- [[reactjs]]
- [[nestjs-auth]]

## Fonti

- `raw/L7_WebML_progettazioneDati.pdf`
- `raw/L9_WebML_progettazioneIpertesto.pdf`
- `raw/L10_Lab_WebML_progettazioneIpertesto.pdf`
