---
tags: [hci, stili, interfaccia, cli, wimp, gui, nl]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/7-Stili di interazione_COMPILED.pdf
---

# Stili di interazione

Gli stili di interazione sono le modalità con cui l'utente comunica con il sistema. Ogni stile ha caratteristiche diverse in termini di apprendibilità, efficienza, espressività e tipo di utente adatto.

## Punti chiave

- **WIMP** (Windows Icons Menus Pointers) è lo stile dominante nei desktop moderni; riduce il carico di memoria usando il riconoscimento.
- **CLI** (Command Line Interface) è lo stile più efficiente per gli esperti ma richiede memorizzazione dei comandi.
- **Linguaggio naturale** è l'interfaccia più "naturale" ma tecnicamente complessa da implementare correttamente.
- Ogni stile ha trade-off tra apprendibilità e efficienza.
- I sistemi moderni spesso combinano più stili (es. IDE con GUI + CLI integrata).

## CLI — Command Line Interface

- L'utente digita comandi testuali; il sistema risponde con testo.
- **Vantaggi**: molto espressivo, efficiente per esperti, facilmente scriptabile.
- **Svantaggi**: alta curva di apprendimento, richiede memorizzazione della sintassi, poco adatto a novizi.
- Esempi: shell Unix/Linux, PowerShell, terminale macOS.

## WIMP — Windows Icons Menus Pointers

Paradigma introdotto da Xerox PARC negli anni '70, poi popularizzato da Apple e Microsoft.
- **Finestre**: aree rettangolari che contengono applicazioni.
- **Icone**: rappresentazioni grafiche di oggetti e azioni.
- **Menu**: liste di comandi disponibili (a tendina, contestuali, a barra).
- **Puntatori**: mouse, trackpad per navigare e selezionare.
- **Vantaggi**: alta apprendibilità grazie alle metafore visive, esplorazione senza memorizzazione.
- **Svantaggi**: meno efficiente della CLI per utenti esperti; dipende dalla metafora del "desktop" che può non adattarsi a tutti i contesti.

## Menu

Classificazione dei menu:
- *A barra*: visibili sempre in cima alla finestra.
- *A tendina* (pull-down): si aprono dalla barra.
- *Contestuali* (pop-up): appaiono con il tasto destro del mouse in risposta al contesto.
- *A torta* (pie menu): organizzati radialmente intorno al cursore.
- *Adattativi*: mostrano prima le opzioni usate di frequente.

La legge di Fitts e la legge di Hick guidano il design dei menu: oggetti grandi e vicini sono più facili da selezionare; meno opzioni → scelta più veloce.

## Form e Fill-in

L'utente compila campi predefiniti. Usato per inserimento dati strutturati (registrazione, ordini). Richiede progettazione attenta delle etichette, dell'ordine dei campi e della validazione degli input.

## Linguaggio naturale

L'utente interagisce tramite testo o voce in linguaggio naturale. Più intuitivo ma difficile da implementare correttamente (ambiguità linguistica). Esempi: assistenti vocali (Siri, Alexa), chatbot, LLM-based UI.

## Direct Manipulation

Interazione diretta con oggetti visivi sullo schermo (drag-and-drop, pinch-to-zoom). Caratteristiche:
- Rappresentazione continua degli oggetti di interesse.
- Azioni fisiche invece di sintassi complessa.
- Feedback immediato e reversibile.
- Alta apprendibilità; adatto a task spaziali.

## Ubiquitous Computing

Paradigma in cui i computer sono integrati nell'ambiente fisico (IoT, wearable, smart home). L'interazione diventa implicita e contestuale. Le interfacce devono essere percettivamente non invadenti e adattarsi al contesto.

## Articoli correlati

- [[modelli-di-interazione]]
- [[la-macchina]]
- [[progettazione-fisica]]
- [[accessibilita]]

## Fonti

- raw/7-Stili di interazione_COMPILED.pdf
