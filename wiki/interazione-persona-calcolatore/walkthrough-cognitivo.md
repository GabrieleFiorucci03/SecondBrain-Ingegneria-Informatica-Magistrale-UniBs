---
tags: [hci, valutazione, walkthrough, cognitivo, apprendibilita]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/13-Walkthrough cognitivo_COMPILED.pdf
---

# Walkthrough cognitivo

Il walkthrough cognitivo è un metodo di ispezione dell'usabilità focalizzato sull'**apprendibilità**: valuta quanto un utente inesperto riesce a capire come usare il sistema eseguendo un task per la prima volta, senza formazione.

## Punti chiave

- Diverso dalla [[valutazione-euristica]]: si concentra su un task specifico passo per passo, non sull'interfaccia in generale.
- Il valutatore simula il ragionamento di un utente novizio: "cosa farebbe un utente la prima volta che incontra questa schermata?"
- Per ogni azione nella sequenza corretta si pongono 4 domande chiave.
- Identifica problemi di apprendibilità che la valutazione euristica potrebbe perdere.
- Richiede: descrizione degli utenti target, lista dei task, sequenza corretta di azioni per ogni task, prototipo o descrizione dell'interfaccia.

## Procedura

1. **Definire gli utenti target**: esperienza, background, motivazioni.
2. **Selezionare i task** da valutare.
3. **Definire la sequenza corretta di azioni** per completare ciascun task.
4. **Walkthrough**: per ogni azione nella sequenza, rispondere alle 4 domande:

### Le 4 domande

Per ogni azione nella sequenza corretta:

1. **L'utente cercherà di raggiungere l'effetto corretto?** — Ha il goal giusto in mente?
2. **L'utente noterà che l'azione corretta è disponibile?** — È visibile il controllo/opzione giusta?
3. **L'utente capirà che l'azione corretta produrrà l'effetto desiderato?** — Il controllo è etichettato in modo comprensibile?
4. **L'utente capirà dal feedback che il task è progredito nella direzione giusta?** — Il sistema risponde in modo interpretabile?

Se la risposta a una domanda è NO, si è trovato un problema di apprendibilità.

## Vantaggi e svantaggi

**Vantaggi**:
- Sistematico e strutturato: riduce la soggettività.
- Utile nelle fasi iniziali del design, con prototipi a bassa fedeltà.
- Non richiede utenti reali.
- Identifica problemi specifici di apprendibilità.

**Svantaggi**:
- Time-consuming se il sistema ha molti task.
- Richiede una sequenza corretta predefinita: non scopre problemi fuori da quella sequenza.
- Meno efficace per valutare efficienza o soddisfazione degli utenti esperti.

## Differenza con la valutazione euristica

| Aspetto | Walkthrough cognitivo | Valutazione euristica |
|---|---|---|
| Focus | Apprendibilità | Usabilità generale |
| Metodo | Task-based, passo-passo | Ispezione globale dell'interfaccia |
| Output | Problemi su azioni specifiche | Lista problemi + severity |
| Utenti simulati | Novizi | Qualsiasi utente |

## Articoli correlati

- [[valutazione-euristica]]
- [[usabilita]]
- [[esperimento-con-utenti]]
- [[ciclo-di-vita]]

## Fonti

- raw/13-Walkthrough cognitivo_COMPILED.pdf
