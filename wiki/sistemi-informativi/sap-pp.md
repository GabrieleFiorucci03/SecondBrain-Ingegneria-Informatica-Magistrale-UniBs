---
tags: [sistemi-informativi, SAP, PP, produzione, MRP, BOM, distinta-base]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/Elena_Andrini_UNIBS Inge 2025 - Pt2 Modulo PP_COMPILED.pdf
  - raw/Elena_Laura_UNIBS Inge 2025 - Laboratorio_COMPILED.pdf
---

# SAP PP — Pianificazione della Produzione

Il modulo SAP **PP (Production Planning)** gestisce l'intero ciclo di pianificazione e controllo della produzione manifatturiera all'interno dell'[[erp|ERP SAP]]. È un modulo verticale settoriale che copre la fase "Fabbricazione" nella catena del valore di Porter. PP è strettamente integrato con MM (Materials Management), SD (Sales & Distribution) e CO (Controlling).

## Punti chiave

- SAP PP si articola in 5 aree: dati tecnico-produttivi, gestione domanda, pianificazione produzione (MRP), pianificazione capacità, controllo produzione.
- I **Materiali** sono le anagrafiche di produzione; ogni materiale ha un tipo (ROH, HALB, FERT) e un tipo di approvvigionamento (E=produzione, F=acquisto).
- La **Distinta Base (BOM)** è la struttura ad albero che descrive i componenti necessari a produrre un materiale; è alla base dell'esplosione MRP.
- **MRP (Material Requirements Planning)**: trasmette i fabbisogni dal prodotto finito fino alle materie prime attraverso BOM e tempi ciclo.
- Transazione MM01 = creazione anagrafica materiale; CS01 = creazione BOM.
- Il laboratorio SAP UniBS usa il sistema **UBD** (UNI BS - Regesta S4).

## Le 5 aree del modulo PP

| Area | Funzione |
|---|---|
| **1. Gestione dati tecnico-produttivi** | Codifica delle anagrafiche di produzione (Materiali, BOM, Cicli di lavoro) |
| **2. Gestione della domanda** | Budget per valutazione del piano di produzione e/o per anticipare la produzione prima della vendita |
| **3. Pianificazione Produzione (MRP)** | Trasmissione dei fabbisogni dal prodotto finito alle materie prime attraverso BOM e tempi ciclo |
| **4. Pianificazione delle capacità** | Monitoraggio della capacità disponibile e del fabbisogno + livellamento |
| **5. Controllo della produzione** | Mantenere allineati i magazzini (versamenti, consumi) + monitoraggio produzione + maturazione costi |

## Anagrafiche materiale

Ogni materiale in SAP ha:
- **Codice materiale**: identificativo univoco (es. PS100, CC100)
- **Tipo materiale**: determina il comportamento del materiale nel sistema
- **Tipo approvvigionamento**: come il materiale viene procurato

### Tipi di materiale

| Codice | Tipo | Descrizione |
|---|---|---|
| **ROH** | Materia Prima | Acquistata esternamente (tipo approv. F = acquisto) |
| **HALB** | Semilavorato | Prodotto internamente o acquistato (tipo approv. E o F) |
| **FERT** | Prodotto Finito | Prodotto internamente (tipo approv. E = produzione) |

### Viste dell'anagrafica materiale (principali)

- **Dati base 1**: codice, descrizione, unità di misura, gruppo merci
- **Pianificazione MRP 1-4**: caratteristica di pianificazione, punto di riordino, orizzonte fisso, lotto
- **Approvvigionamento MRP 2**: tipo approvvigionamento, tempi di schedulazione, stock di sicurezza
- **Contabilità 1-2**: valutazione del materiale (classe di valutazione, prezzo standard)

**Transazione**: `MM01` (creazione), `MM02` (modifica), `MM03` (visualizzazione)

## Distinta Base (BOM — Bill of Materials)

La BOM è la struttura ad albero che elenca tutti i componenti (con quantità e unità di misura) necessari per produrre un materiale padre. Può avere più livelli gerarchici.

**Esempio — Penna a sfera colorata (PS100):**

```
PS100 — Penna a sfera colore 100 (FERT)
├── SC100 — Serbatoio pieno colore 100 (HALB)
│   ├── SV999 — Serbatoio vuoto standard (ROH)
│   └── IN100 — Inchiostro colore 100 (ROH)
├── TC100 — Tappo colore 100 (HALB)
│   ├── TN999 — Tappo neutro (ROH)
│   └── IN100 — Inchiostro colore 100 (ROH)
├── CC100 — Copertura colorata 100 (HALB)
│   ├── CS999 — Copertura standard (ROH)
│   └── IN100 — Inchiostro colore 100 (ROH)
├── BE999 — Becco standard (ROH)
├── SF999 — Sfera standard (ROH)
└── FU999 — Fusto standard (ROH)
```

**Naming convention nel laboratorio:**

| Componente | Codice | Tipo |
|---|---|---|
| Penna a sfera colore 001-100 | PS001–PS100 | FERT, Produzione (E) |
| Tappo colorato colore 001-100 | TC001–TC100 | HALB, Produzione (E) |
| Serbatoio pieno colore 001-100 | SC001–SC100 | HALB, Produzione (E) |
| Copertura colorata colore 001-100 | CC001–CC100 | HALB, Produzione (E) |
| Inchiostro colore 001-100 | IN001–IN100 | ROH, Acquisto (F) |
| Becco standard | BE999 | ROH, Acquisto (F) |
| Fusto standard | FU999 | ROH, Acquisto (F) |
| Sfera standard | SF999 | ROH, Acquisto (F) |
| Serbatoio vuoto standard | SV999 | ROH, Acquisto (F) |
| Tappo neutro | TN999 | ROH, Acquisto (F) |
| Copertura standard | CS999 | ROH, Acquisto (F) |

**Transazione**: `CS01` (creazione BOM), `CS02` (modifica), `CS03` (visualizzazione)

## Procedura lab: creazione codice materiale (MM01)

1. Richiamare transazione `MM01` nella command box
2. Inserire codice materiale (es. `CCxxx`), Settore industriale = M, Tipo materiale = HALB
3. "Copiare da" materiale modello (es. CC100)
4. Selezionare le viste necessarie → premere "Liv. org."
5. Confermare Divisione (2310 = Plant 1 IT), Magazzino (231A)
6. Navigare le viste con INVIO, modificare la descrizione
7. Salvare

## Procedura lab: creazione BOM (CS01)

1. Richiamare transazione `CS01`
2. Inserire codice materiale padre, Divisione = 2310, Utilizzo = 1 (Produzione)
3. Aggiungere posizioni: Tipo posizione = L, componente (codice), quantità, UM
4. Salvare

## Articoli correlati

- [[erp]]
- [[erp-evoluzione]]
- [[processi-aziendali]]

## Fonti

- raw/Elena_Andrini_UNIBS Inge 2025 - Pt2 Modulo PP_COMPILED.pdf
- raw/Elena_Laura_UNIBS Inge 2025 - Laboratorio_COMPILED.pdf
