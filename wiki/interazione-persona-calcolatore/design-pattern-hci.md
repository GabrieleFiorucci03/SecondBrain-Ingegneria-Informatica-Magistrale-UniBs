---
tags: [hci, design-pattern, riuso, interfaccia, soluzione-riusabile]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/17-Design-pattern-HCI_COMPILED.pdf
---

# Design Pattern per HCI

I design pattern in HCI sono soluzioni riusabili a problemi ricorrenti di progettazione dell'interazione. Adattano il concetto di pattern (nato nell'architettura con Alexander, poi nell'ingegneria del software con la GoF) al dominio dell'interfacce utente.

## Punti chiave

- Un pattern descrive: il **problema** ricorrente, il **contesto** in cui si presenta, la **soluzione** consolidata.
- I pattern non sono regole rigide ma linee guida che si adattano al contesto specifico.
- I cataloghi di pattern (es. Tidwell "Designing Interfaces") raccolgono soluzioni validate da pratiche di design consolidate.
- Usando pattern noti si riduce il tempo di design e si sfrutta l'esperienza accumulata dalla comunità.
- I pattern HCI operano a diversi livelli: navigazione, layout, form/input, feedback, ricerca.

## Struttura di un pattern

Ogni pattern include tipicamente:
- **Nome**: identificatore mnemonico (es. "Breadcrumb", "Infinite Scroll").
- **Problema**: situazione in cui il pattern si applica.
- **Contesto**: condizioni al contorno.
- **Soluzione**: descrizione della struttura e del comportamento.
- **Esempi**: casi reali di applicazione.
- **Conseguenze**: vantaggi e svantaggi dell'applicazione.

## Categorie di pattern HCI

### Navigazione
- **Breadcrumb**: mostra il percorso gerarchico dalla radice alla pagina corrente. Riduce il disorientamento.
- **Tab navigation**: organizza i contenuti in schede; visibilità immediata delle sezioni.
- **Pagination vs Infinite Scroll**: pagination per contenuti tabulari con ricerca; infinite scroll per feed continui.

### Form e input
- **Autocomplete**: suggerisce completamenti mentre l'utente digita; riduce errori e velocizza l'inserimento.
- **Inline validation**: valida i campi mentre l'utente li compila (non solo alla submission).
- **Wizard**: suddivide un form complesso in passi sequenziali; riduce il senso di sovraccarico.

### Feedback e stati
- **Progress indicator**: informa l'utente sullo stato di un'operazione lunga (barra, spinner, step counter).
- **Empty state**: schermata dedicata quando non ci sono dati; guida l'utente su come iniziare.
- **Skeleton screen**: placeholder che simula il layout durante il caricamento; migliore perceived performance rispetto allo spinner.

### Ricerca
- **Search-as-you-type**: risultati aggiornati in tempo reale mentre l'utente digita.
- **Faceted search / Filter**: filtri multipli per restringere i risultati.

## OOUX — Object-Oriented UX

Metodologia di Sophia Prater per strutturare il design concettuale e fisico:
1. Identificare gli **oggetti** del dominio (es. in un e-commerce: prodotto, ordine, utente).
2. Definire gli **attributi** di ciascun oggetto.
3. Definire le **azioni** su ciascun oggetto (core, supporting, machine actions).
4. Mappare le **relazioni** tra oggetti.
5. Costruire il design fisico a partire da questa struttura.

OOUX garantisce consistenza tra il modello concettuale e il design fisico dell'interfaccia.

## Articoli correlati

- [[progettazione-concettuale]]
- [[progettazione-fisica]]
- [[ciclo-di-vita]]
- [[principi-di-nielsen]]

## Fonti

- raw/17-Design-pattern-HCI_COMPILED.pdf
