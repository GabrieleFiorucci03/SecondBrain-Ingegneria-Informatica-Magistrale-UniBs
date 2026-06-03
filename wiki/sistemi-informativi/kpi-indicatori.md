---
tags: [sistemi-informativi, KPI, indicatori, misurazione-prestazioni, SID]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/KPI-INTRO.pdf
---

# KPI (Key Performance Indicators)

I KPI (Key Performance Indicators) sono misure di prestazione utilizzate per valutare l'esito di attività tattiche o strategiche in un'organizzazione. La caratteristica distintiva è fornire **informazioni riassuntive immediatamente interpretabili e consumabili**. Scopo del metodo: individuare un numero ridotto di indicatori che misurino le prestazioni di un dato processo aziendale (efficienza ed efficacia).

## Punti chiave

- **Efficacia** = Risultati/Obiettivi (grado di raggiungimento degli obiettivi).
- **Efficienza** = Risultati/Risorse (costo del raggiungimento degli obiettivi).
- I KPI si dividono in: **indicatori generali** (volumi di input/output/risorse) e **indicatori competitivi** (costi, qualità, tempi).
- Le misure devono essere: **rilevanti** (collegate alla strategia), **semplici** (essenziali, facili da spiegare), **bilanciate** (economiche e non), **dinamiche** (adattabili, accessibili, tempestive).
- Il processo è visto come una black box: Input → Processo → Output, con Risorse.
- Ogni KPI ha: Classe, Indicatore standard, Varianti standard, Metrica standard, Unità di misura.

## Il metodo KPI

### Il processo come black box

```
Input (richieste del cliente, input materiali)
          ↓
     [PROCESSO]  ← Risorse (personale, impianti, scorte, informazioni)
          ↓
Output (beni e servizi al cliente)
```

- **Input**: richieste di servizio e input materiali (ordini, pezzi da consegnare)
- **Output**: beni o servizi prodotti destinati al cliente
- **Risorse**: personale, materie prime, impianti, magazzini

### Procedura degli esercizi KPI

1. **Identificazione dei CSF** (Critical Success Factors) per il processo
2. **Identificazione e caratterizzazione dei KPI**:
   - Classe KPI (es., costi ed efficienza)
   - Identificazione varianti standard (es., costo unitario input/output)
   - Fonti informative (source data)
   - Metrica (formula, es.: consegne_errate × 100 / consegne_totali)
   - Unità di misura (es., percentuale)
3. **Analisi dimensionale**: per ciascun KPI, individuare e documentare le dimensioni applicabili (gerarchie dimensionali)

## Categorie di KPI

### Schema gerarchico

```
KPI
├── Indicatori Generali (contesto: volumi)
│   ├── Input: ordini/richieste, modifiche, cancellazioni
│   ├── Output: prodotti/servizi, informazioni, conferme
│   └── Risorse: risorse umane, impianti, scorte, tecnologie
└── Indicatori Competitivi
    ├── Costi ed Efficienza
    ├── Qualità
    └── Tempi di Servizio
```

### 1. Indicatori Generali

Misurano i volumi di input/output e delle risorse del processo.

| Indicatore | Varianti | Misure |
|---|---|---|
| Volumi input | Ordini/richieste; modifiche; cancellazioni | €, # |
| Volumi output | Conferme; prodotti materiali e immateriali | €, # |
| Risorse umane | Organico; presenze; stipendi | €, #, FTE |
| Impianti | Variabili a seconda del settore | €, #, Capacità |
| Scorte | Variabili a seconda del settore | €, # |
| Altre risorse | Tecnologie e conoscenze; magazzini | Byte, #documenti |

### 2. Indicatori di Costi ed Efficienza

| Indicatore | Varianti | Metrica |
|---|---|---|
| Costo unitario | Costo unitario input | Costi/input |
|  | Costo unitario output | Costi/output |
|  | Costo monetario al cliente | Costo totale/output |
| Produttività | Produttività risorse umane | Prodotto/addetti |
|  | Produttività impianti | Prodotto/impianti |
|  | Rotazione scorte | Vendite/scorte |
|  | Produttività nel tempo | Prodotto/tempo lavoro |
| Utilizzo | Saturazione risorse umane | [Risorse utilizzate/disponibili]×100% |
|  | Saturazione impianti | [Impianti utilizzati/disponibili]×100% |
|  | Saturazione scorte | [Scorte utilizzate/disponibili]×100% |
|  | Ottimizzazione del tempo | [Tempo operativo/tempo totale]×100% |
|  | Utilizzo dell'informazione | [Info utilizzata/info disponibile]×100% |

### 3. Indicatori di Qualità

| Indicatore | Varianti | Metrica |
|---|---|---|
| Conformità | Interna: scarti o rilavorazioni | [Output scartati/output totali]×100% |
|  | Interna: costi rilavorazione | [Costi rilavorazione/costi totali]×100% |
|  | Esterna: reclami | [Reclami cliente/output totali]×100% |
|  | Esterna: resi | [Output resi/output totali]×100% |
|  | Flessibilità impianto | [Input anomali serviti/output totali]×100% |
| Affidabilità | Affidabilità processo produttivo | MTBF; MTTR |
| Soddisfazione cliente | Tasso clienti soddisfatti | [Soddisfatti/totali]×100% |

**MTBF** = Mean Time Between Failures; **MTTR** = Mean Time To Repair.

### 4. Indicatori di Tempi di Servizio

| Indicatore | Metrica | Misura |
|---|---|---|
| Puntualità (ritardi) | [Output in ritardo/output totali]×100% | % |
| Entità dei ritardi | [Ritardo/tempo assoluto]×100% | % |
| Tempo di risposta/lead time | Tempo di gestione del processo (media sull'output) | Tempo |
| Tasso evasione richieste | [Richieste non accettate/richieste totali]×100% | % |
| Flessibilità verso il cliente | [Modifiche servite in tempo/modifiche totali]×100% | % |

## Dimensioni dei KPI

Gli indicatori sono **segmentabili rispetto a dimensioni** (es., un indicatore "tempo di risposta" è tipicamente segmentato per cliente o servizio).

### Dimensioni standard

| Dimensione | Scopo | Granularità | Gerarchie |
|---|---|---|---|
| **Tempo** | Standard | Singola data | Mese/Trimestre/Anno |
| **Responsabilità** | Centri di responsabilità aziendali | Singolo addetto | Struttura responsabilità; area geografica |
| **Cliente** | Clienti e segmenti | Singolo cliente | Classif. commerciale; geografica |
| **Processo** | Processi fondamentali | Attività/processo | Gerarchia processi |
| **Prodotto** | Modelli e versioni prodotti | Singolo articolo | Classif. commerciale |

### Proprietà delle dimensioni

| Proprietà | Descrizione |
|---|---|
| **Granularità/dettaglio** | Minimo livello di dettaglio nella dimensione (es., mese, giorno) |
| **Tipologia** | Sottotipi di elementi (es., centro di costo vs. centro di profitto) |
| **Cardinalità** | Numero di elementi nella dimensione (es., 24 mesi) |
| **Gerarchie** | Aggregazioni e generalizzazioni applicabili |

## Articoli correlati

- [[tipologie-si]]
- [[business-intelligence]]
- [[data-warehouse]]
- [[bpr]]
- [[crm]]

## Fonti

- raw/KPI-INTRO.pdf
