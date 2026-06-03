---
tags: [sistemi-informativi, BPR, processi-aziendali, innovazione, progettazione]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L2-SI-PROG.pdf
---

# Business Process Reengineering (BPR)

Il Business Process Reengineering è un approccio strutturato all'innovazione organizzativo-gestionale orientato al raggiungimento di **miglioramenti radicali nelle prestazioni** tramite il ridisegno dei processi aziendali. Non è un progetto una tantum, ma un percorso continuo di innovazione e ottimizzazione.

## Punti chiave

- Obiettivo: eliminare ridondanze, attività e flussi che non generano valore.
- Migliora efficacia e performance su costi, output, servizio, velocità e qualità.
- È uno dei quattro paradigmi di [[progettazione-si]] (usato per SI legacy/ereditati).
- Il processo BPR si articola in tre macro-fasi: sviluppo visione strategica → riprogettazione dei processi → realizzazione del cambiamento.
- L'IT è il catalizzatore del cambiamento: la riprogettazione tecnologica e organizzativa va fatta congiuntamente.
- Metodologia AS-IS / TO-BE: analisi dello stato attuale → definizione dello stato futuro desiderato.

## Fasi del BPR

```
[GESTIONE DEL PROGETTO BPR]  ←→  [SVILUPPO VISIONE STRATEGICA]
                                           ↓
                               [RIPROGETTAZIONE DEI PROCESSI]
                               ├── Mappatura dei processi (AS-IS)
                               ├── Analisi e diagnosi
                               └── Ridisegno processi (TO-BE)
                                           ↓
                               [REALIZZAZIONE DEL CAMBIAMENTO]
                               ├── Implementazione cambiamento
                               └── Monitoraggio prestazioni
```

### 1. Sviluppo visione strategica
- Definizione degli obiettivi di miglioramento (es. riduzione tempi consegna, aumento soddisfazione clienti)
- Identificazione dei processi critici da riprogettare

### 2. Riprogettazione dei processi
- **Mappatura AS-IS**: analisi del flusso attività attuale, identificazione dei problemi
- **Analisi e diagnosi**: individuazione di inefficienze, colli di bottiglia, ridondanze
- **Ridisegno TO-BE**: definizione del nuovo flusso di attività ottimizzato

### 3. Realizzazione del cambiamento
- **Implementazione**: introduzione del nuovo sistema (spesso tramite ERP), formazione del personale, nuove politiche operative
- **Monitoraggio**: misurazione delle prestazioni tramite [[kpi-indicatori]] (tempi di consegna, livello di servizio, rotazione magazzino, reclami clienti)

## Caso Ford: esempio paradigmatico

**AS-IS**: Il reparto contabilità fornitori di Ford Nord America contava 500 persone. Il processo prevedeva tre documenti cartacei da incrociare: ordine d'acquisto, nota di accompagnamento (XAB + Buono Entrata), fattura. Il 20% degli ordini e consegne impegnava l'80% del tempo per risolvere le discordanze tra documenti.

**TO-BE**: Eliminazione totale della fattura come documento. Quando il compratore emette un ordine, lo inserisce direttamente in una base dati condivisa. All'arrivo della merce, il ricevimento verifica la corrispondenza con l'ordine nel DB: se corrisponde, il pagamento parte automaticamente.

**Risultato**: da 500 addetti a **125** (−75%), eliminazione dei flussi cartacei, riduzione del leadtime, pagamento su avanzamento programma.

**Lesson learned**: non basta automatizzare il processo esistente (avrebbe portato al −20%), bisogna **ripensare l'intero processo**.

## Fasi IBM (2024)

IBM descrive il BPR come un ciclo a 6 fasi:
1. Definizione degli obiettivi
2. Valutazione dello stato attuale (AS-IS)
3. Identificazione di lacune e opportunità
4. Sviluppo dello stato futuro (TO-BE)
5. Implementazione dei cambiamenti
6. Valutare e ripetere

## Articoli correlati

- [[progettazione-si]]
- [[erp]]
- [[kpi-indicatori]]
- [[introduzione-si]]

## Fonti

- raw/L2-SI-PROG.pdf
