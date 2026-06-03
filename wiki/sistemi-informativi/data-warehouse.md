---
tags: [sistemi-informativi, data-warehouse, OLAP, ETL, SID, ipercubo, star-schema]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/8-SIA-SID-IntroIndicatori.pdf
---

# Data Warehouse

Il Data Warehouse (DW) è la tecnologia di storage centrale dei [[tipologie-si|Sistemi Informativi Direzionali (SID)]]. È una collezione di dati **integrata, persistente, variabile nel tempo e orientata a un certo argomento**, creata e mantenuta per supportare decisioni manageriali e analisi dei dati (Inmon, 1996). Si contrappone al DB transazionale (OLTP) con un paradigma analitico (OLAP).

## Punti chiave

- Il DW raccoglie dati da sorgenti eterogenee (BD dipartimentali, archivi legacy, fonti esterne) tramite processo **ETL** (Extract, Transform, Load).
- I dati nel DW sono organizzati in una struttura **multidimensionale (ipercubo / datacube)**: fatti × dimensioni.
- Le operazioni OLAP sul DW sono: **roll-up** (aggrega), **drill-down** (disaggrega), **slice & dice** (seleziona), **pivot** (re-orienta).
- Due tipi di server OLAP: **MOLAP** (strutture a matrice multidimensionale) e **ROLAP** (RDBMS con schema stella/fiocco di neve).
- Lo schema **a stella** ha una tabella centrale dei fatti collegata a tabelle di dimensione piatte.
- Lo schema **a fiocco di neve** normalizza le tabelle dimensione in più livelli gerarchici.

## Caratteristiche dei SID

Un SID (Sistema Informativo Direzionale) si distingue da un SIO per tre proprietà fondamentali dell'informazione che elabora:

| Caratteristica | Descrizione | Esempio |
|---|---|---|
| **Aggregazione** | Elabora indicatori, non singole transazioni | Vendite totali di un magazzino, non singolo scontrino |
| **Tempificazione** | Analizza informazioni con periodicità | Vendite totali di un semestre/trimestre/mese |
| **Multidimensionalità** | Molteplici segmentazioni | Vendite per prodotto/regione/periodo a diversi livelli gerarchici |

Il SID supporta due funzioni: **DSS** (definizione obiettivi) e **reporting** (valutazione risultati effettivi).

**Esempio di rendiconto direzionale:**
```
              SEM 2 (EFF | BDGT)  |  ANNO (EFF | BDGT)  |  PROD 1 | PROD 2
Vendite       2.100 | 2.000       |  4.300 | 4.000       |  1.955  | 2.345
Primo Margine   530 |   480       |  1.300 |   850       |    255  | 1.045
Utile           110 |    35       |    231 |    74       |    154  |    77
```

## Architettura del SID

```
[Motori calcolo DSS] [Motori presentazione EIS/report] [Data Mining]
                              ↓
                         DATA MART
                              ↓
                        DATA WAREHOUSE
                         ↑          ↑
              [ETL]                  [Data Entry direzionale]
              ↑                      [Acquisizione dati esterni]
    BASI DATI TRANSAZIONALI (OLTP)
```

- **ETL**: Estrazione → Trasformazione → Caricamento (Loading)
- **Data Mart**: subset del DW orientato a una specifica area (es. marketing, vendite)
- **Data Entry direzionale**: obiettivi, budget inseriti manualmente

### Dati presenti in ERP (fonte primaria)
- Ordini di vendita (numero ordine, data, cliente, prodotto, quantità, prezzo, sconto)
- Anagrafica prodotti (codice, categoria, fornitore)
- Anagrafica clienti (codice, ragione sociale, settore, regione)
- Fatturazione (numero fattura, data, importi)

### Dati NON presenti in ERP (fonti esterne)
- Feedback e recensioni clienti sui prodotti
- Dati di posizionamento e traffico sito web e-commerce
- Informazioni meteo e stagionalità
- Dati demografici e di profiling (età, sesso, reddito)
- Informazioni sulla concorrenza e prezzi di mercato

## Il modello multidimensionale (Ipercubo)

Un **indicatore** (es. "quantità venduta") è descritto da n **chiavi** che identificano **dimensioni di analisi**:
- tempo#, prodotto#, punto_vendita# → indicatore "quantità venduta"

**Ipercubo**: spazio n-dimensionale dove ogni cella contiene il valore dell'indicatore per quella combinazione di dimensioni.

```
Dimensione prodotto
      ↑
      |    [cella: Prodotto2, Giorno2, PV1 = volume vendita]
      |  ████████████████████████
      | ███████████████████████
      |██████████████████████
      +────────────────────────→ Dimensione tempo
                              ↗
                         Dimensione punto vendita
```

- **Sparsità** (S) = celle piene / celle totali
- **C-totale** = C(D₁) × C(D₂) × ... × C(Dₙ) (prodotto delle cardinalità)

**Esempio**: Giorni=730 × Prodotti=10.000 × Punti vendita=350 = 2,555 miliardi di celle

## Processo ETL

| Fase | Descrizione |
|---|---|
| **Extract (Estrazione)** | Lettura dati grezzi dai sistemi sorgente |
| **Transform (Trasformazione)** | Calcolo nuovi attributi, pulizia, normalizzazione (es. calcolare "Sconto Applicato" = sconto/prezzo originale × 100) |
| **Load (Caricamento)** | Inserimento dati trasformati nella tabella dei fatti del DW |
| *(Area di Staging)* | Zona temporanea dove i dati estratti vengono memorizzati prima delle trasformazioni |

## Operazioni OLAP

| Operazione | Descrizione | Esempio |
|---|---|---|
| **Roll-up** | Aggrega i dati salendo nella gerarchia | Vendite totali dell'anno per categoria e regione |
| **Drill-down** | Disaggrega i dati scendendo nella gerarchia | Per categoria/regione → vendite giornaliere per negozio |
| **Slice & Dice** | Seleziona e proietta sottoinsiemi del cubo | Solo prodotti alimentari, solo Q3 |
| **Pivot** | Re-orienta il cubo su dimensioni indicate | Scambia righe e colonne di una tabella pivot |

## Server OLAP

### MOLAP (Multidimensional OLAP)
- Implementa direttamente il modello a cubo tramite strutture a matrice multidimensionale
- Ottimo per query veloci su dataset non troppo grandi

### ROLAP (Relational OLAP) — più diffuso
- Usa un RDBMS standard per realizzare la struttura multidimensionale
- Interrogazioni SQL con estensioni per operazioni dimensionali (drill-down, roll-up)
- Schema **a stella** o **a fiocco di neve**

**Schema a stella:**
```
     [Dimensione Tempo]         [Dimensione Prodotto]
           ↑                           ↑
     [TABELLA FATTI: Vendite] ──────────
           ↓                           ↓
     [Dimensione Luogo]         [Dimensione Cliente]
```
- **Tabella centrale dei fatti**: righe = dati da analizzare, con FK alle dimensioni + metriche (Qtà, Incasso)
- **Tabelle dimensione**: parametri per le analisi (Tempo: ora/giorno/.../anno; Prodotto: categoria/modello)

**Schema a fiocco di neve**: dimensioni normalizzate su più livelli gerarchici (es. Luogo → Negozio → Città → Regione → Stato)

## Articoli correlati

- [[tipologie-si]]
- [[business-intelligence]]
- [[kpi-indicatori]]
- [[erp]]

## Fonti

- raw/8-SIA-SID-IntroIndicatori.pdf
