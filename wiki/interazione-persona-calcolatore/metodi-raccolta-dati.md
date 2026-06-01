---
tags: [hci, raccolta-dati, intervista, questionario, osservazione, think-aloud]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/22-Metodi-raccolta-dati_COMPILED.pdf
---

# Metodi di raccolta dati

I metodi di raccolta dati in HCI servono sia per l'analisi dei requisiti (capire gli utenti prima del design) sia per la valutazione dell'usabilità (capire problemi durante/dopo il design). Vanno combinati per triangolare i risultati.

## Punti chiave

- Tre categorie principali: **osservazione**, **raccolta di opinioni** (interviste/questionari), **monitoraggio** (logging).
- Il think-aloud è la tecnica di osservazione più usata nei test di usabilità.
- Interviste e questionari raccolgono *opinioni* — ciò che l'utente pensa — non comportamenti reali.
- I questionari standardizzati (SUS, PSSUQ) permettono confronti tra sistemi diversi.
- È utile e consigliato **combinare più tecniche** (triangolazione).

## Osservazione

**Diretta**: il valutatore è presente mentre l'utente usa il sistema.
**Indiretta**: con video/audio registrati, analizzati in seguito.
**Field studies / in-the-wild studies**: osservazione nel contesto naturale dell'utente (sul lavoro, a casa). Scopre modi d'uso inaspettati. Richiede consenso informato.

### Protocolli verbali

Focus su ciò che l'utente *dice*:

- **Think-aloud ("Thinking aloud")**: si chiede all'utente di commentare ad alta voce mentre usa il sistema.
  - Vantaggi: raccoglie dati qualitativi ricchi; rivela modelli mentali e teorie ingenue dell'utente.
  - Svantaggi: carico cognitivo aggiuntivo; effetto Hawthorne (comportamento non naturale).
  - Conduzione: lo sperimentatore stimola l'utente a parlare senza rispondere alle richieste; si fa una demo prima per ridurre il disagio.
  
- **Protocollo spontaneo**: raccogliere commenti che l'utente fa naturalmente (meno invasivo, meno dati).

- **Post-event protocol**: l'utente commenta a posteriori guardando la registrazione della sessione; elimina il carico cognitivo durante il task ma può perdere dettagli.

**Effetto Hawthorne**: le persone modificano il comportamento sapendo di essere osservate.

## Interviste

- Dà indicazioni **qualitative**: esplora atteggiamenti, opinioni, motivazioni.
- Presenza di un intervistatore che legge domande e registra le risposte.
- Risultati immediati; più flessibile del questionario.
- Strumento di **semantizzazione progressiva**: le prime interviste aiutano a capire il vocabolario degli utenti.
- Limite: le risposte riflettono ciò che l'utente *pensa* che sia vero, non necessariamente la realtà.

## Questionari

- Più verso il quantitativo; può raggiungere molti utenti.
- **Difetto**: rigidità — le domande non si adattano alle risposte.
- **Domande aperte**: risposta libera; difficile da analizzare.
- **Domande chiuse**: risposta da un insieme limitato.
- Occorre un **test pilota** prima del deployment per ridurre ambiguità.
- Vantaggi: scalabile; può raggiungere utenti impossibilitati a partecipare a interviste.

### Scale per questionari

| Scala | Caratteristiche | Statistica corretta |
|---|---|---|
| Lista di controllo | No ordine, zero, distanza | Frequenza |
| Rating scale | Ordine, no zero/distanza | Mediana |
| Likert | Accordo/disaccordo su affermazioni | Mediana (o media con cautela) |
| Differenziale semantico | Aggettivi bipolari agli estremi | Mediana |
| Ordinale per rango | Ordinare elementi | Mediana |

**Scala Likert**: es. "Completamente in disaccordo / Disaccordo / Neutrale / D'accordo / Completamente d'accordo". Spesso convertita in scala numerica (-3 a +3) per analisi statistiche.

### Questionari standardizzati

- **SUS** (System Usability Scale, Brooke 1986): 10 domande; produce un punteggio comparabile con benchmark di riferimento.
- **PSSUQ / CSUQ** (Lewis 1995): 16 domande; misura soddisfazione, qualità dell'interfaccia e della documentazione.

## Monitoraggio (Logging)

- Registrazione automatica delle interazioni utente-sistema (click, sequenze di azioni, tempi).
- Non invasivo; dati oggettivi e di grandi dimensioni.
- Limite: registra *cosa* fanno gli utenti, non *perché*.

## Articoli correlati

- [[esperimento-con-utenti]]
- [[ciclo-di-vita]]
- [[usabilita]]
- [[valutazione-euristica]]

## Fonti

- raw/22-Metodi-raccolta-dati_COMPILED.pdf
