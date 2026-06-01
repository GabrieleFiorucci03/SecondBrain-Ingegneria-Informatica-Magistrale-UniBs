---
tags: [hci, utente, mhp, memoria, percezione, profilo, persona]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/3-Utente-parte-1_COMPILED.pdf
  - raw/4-Utente-parte-2_COMPILED.pdf
  - raw/5-Profilo-utente_COMPILED.pdf
---

# L'utente

La comprensione delle caratteristiche cognitive, percettive e fisiche degli utenti è fondamentale nel design HCI. Il Model Human Processor (MHP) di Card, Moran e Newell è il framework di riferimento per modellare le capacità umane in interazione con i sistemi.

## Punti chiave

- **Model Human Processor (MHP)**: tre sottosistemi — percettivo, cognitivo, motorio — ognuno con proprio tempo di ciclo, capacità e tempo di decay della memoria.
- **Memoria a breve termine** (working memory): capacità 7±2 chunk, decay ~7 secondi; bottleneck fondamentale nel design di interfacce.
- **Memoria a lungo termine**: praticamente illimitata, organizzata in rete semantica; l'accesso richiede riconoscimento o richiamo.
- **Percezione visiva**: il sistema visivo ha un campo periferico ampio ma acuità concentrata nella fovea; i principi Gestalt (prossimità, similarità, chiusura) guidano il raggruppamento percettivo.
- **Profilo utente**: principiante, esperto e utente abituale hanno esigenze diverse; il design deve tenere conto di questa variabilità.
- **Personas**: archetipi di utenti creati da ricerca, utili per guidare le decisioni di design.

## Model Human Processor

| Sistema | Tempo di ciclo | Capacità | Decay |
|---|---|---|---|
| Percettivo | ~100ms | 7 chunks | ~200ms |
| Cognitivo | ~70ms | 7±2 chunks (WM) | ~7s (WM) |
| Motorio | ~70ms | — | — |

Il sistema cognitivo opera sulla working memory (WM) — la "RAM" del cervello. Superare i limiti della WM genera errori e carico cognitivo elevato.

## Sistema percettivo: visione

- **Campo visivo**: ~200° orizzontale, 150° verticale; area foveale (acuità massima) ~2°.
- **Costanza percettiva**: percepiamo oggetti come stabili nonostante variazioni di luce, angolo, distanza.
- **Principi Gestalt**: prossimità, similarità, continuità, chiusura, figura-sfondo — usati per organizzare layout visivi.
- **Visione dei colori**: 3 tipi di coni (R, G, B); il daltonismo colpisce ~7% degli uomini.
- **Lettura**: non è lineare; gli occhi fanno saccadi e fissazioni; il testo denso rallenta la lettura.

## Memoria

**Sensoriale**: buffer brevissimo (<1s) per stimoli visivi (iconica) e uditivi (ecoica).

**A breve termine / Working Memory**:
- Capacità: 7±2 chunk (Miller, 1956); chunk è un'unità significativa (non singoli byte).
- Decay: ~7 secondi senza rehearsal.
- Implicazione di design: non costringere l'utente a ricordare informazioni tra schermate diverse.

**A lungo termine**:
- *Semantica*: fatti, concetti, regole.
- *Episodica*: eventi personali.
- *Procedurale*: come fare le cose (skill).
- Accesso: riconoscimento (più facile) vs richiamo (più difficile). → preferire il riconoscimento nel design (es. menu invece di riga di comando).

## Ragionamento e Problem Solving

- **Deduttivo**: dalla regola al caso specifico.
- **Induttivo**: dal caso specifico alla regola (es. imparare a usare un sistema per tentativi).
- **Abduttivo**: dalla situazione alla causa più probabile (es. diagnosticare errori).
- Gli utenti spesso non ragionano logicamente ma usano euristiche e analogie.

## Profilo utente e Personas

**Classi di utenti**:
- *Principiante*: necessita di guide, feedback espliciti, errori tollerati.
- *Esperto*: vuole efficienza, scorciatoie, minimo rumore visivo.
- *Utente abituale*: ha automatizzato procedure, ma può essere confuso da cambiamenti.

**Personas**: descrizioni fittizie ma basate su dati reali di tipici utenti. Includono nome, età, obiettivi, frustrazioni, contesto d'uso. Servono per focalizzare le decisioni di design su utenti reali anziché ipotetici.

**Analisi dei compiti (HTA — Hierarchical Task Analysis)**: scomposizione gerarchica dei compiti in sottocompiti e operazioni. Utile per capire cosa gli utenti devono fare con il sistema.

## Articoli correlati

- [[introduzione-hci]]
- [[profilo-utente]]
- [[accessibilita]]
- [[ciclo-di-vita]]

## Fonti

- raw/3-Utente-parte-1_COMPILED.pdf
- raw/4-Utente-parte-2_COMPILED.pdf
- raw/5-Profilo-utente_COMPILED.pdf
