---
tags: [sistemi-informativi, SIO, SID, OLTP, OLAP, data-warehouse]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L1-SI-INTRO.pdf
---

# Tipologie di Sistemi Informativi Aziendali

I sistemi informativi aziendali si classificano in due macro-categorie complementari e interrelate: i **Sistemi Informativi Operativi (SIO)** e i **Sistemi Informativi Direzionali (SID)**. I SIO alimentano i SID: i dati transazionali vengono aggregati e trasformati in informazioni direzionali.

## Punti chiave

- **SIO** = elaborazione transazioni (livello operativo); basati su OLTP con DB transazionali
- **SID** = reporting e supporto a decisioni (livello tattico/strategico); basati su OLAP con Data Warehouse
- I SIO producono i dati grezzi che, aggregati, alimentano i SID
- Ogni livello ha esigenze informative radicalmente diverse: dettaglio puntuale per SIO, sintesi e tendenze per SID
- I sistemi SID includono: MIS (Management Information Systems), DSS (Decision Support Systems), EIS (Executive Information Systems)

## Sistemi Informativi Operativi (SIO)

I SIO sono **sistemi di elaborazione dati** che supportano il livello operativo dell'azienda. Funzioni principali:

- **Anagrafiche digitali**: schedari elettronici di prodotti, clienti, fornitori
- **Elaborazione transazioni**: sportello, ordini, operazioni contabili
- **Pianificazione operazioni**: programmazione produzione, gestione scorte
- **Gestione procedure amministrative**: contabilità clienti, fornitori

### Architettura OLTP (On-Line Transaction Processing)

```
PROCESSO 1 | PROCESSO 2 | ... | PROCESSO N
              ↕         ↕              ↕
           +---------------------------------+
           |         BASE DI DATI           |
           +---------------------------------+
```

- La **base di dati** memorizza le informazioni persistenti
- N processi di elaborazione leggono e/o scrivono sulla base di dati tramite **transazioni**
- Caratteristiche: alta frequenza di operazioni, operazioni semplici e veloci, consistenza garantita (ACID)

**Esempio SIO in azienda manifatturiera:**
- Anagrafiche digitali per prodotti
- Elaborazione transazioni e controllo cicli di approvvigionamento, produzione, vendita
- Sistemi di supporto amministrativo (contabilità)

## Sistemi Informativi Direzionali (SID)

I SID supportano il livello tattico e strategico. Si dividono in:

### Sistemi di Reporting Direzionale
- Forniscono informazioni a diversi livelli di sintesi, in modo flessibile e non pre-definito
- Consentono modifiche veloci di formato e contenuto (rendiconti analitici diversificati)

### Sistemi di Supporto a Decisioni
- Supporto ad attività di controllo e pianificazione strategica
- **MIS** (Management Information Systems): informazioni aggregate per i manager
- **DSS** (Decision Support Systems): motori di calcolo per analisi what-if e simulazioni
- **EIS** (Executive Information Systems): cruscotti per l'alta direzione

### Architettura SID

```
[DSS - Motori di Calcolo]   [EIS - GUI/Report/Query]
              ↕                        ↕
     +-------------------------------------------+
     |      DATA WAREHOUSE (OLAP)                |
     +-------------------------------------------+
              ↑                        ↑
     [Data Entry direzionale]  [AGGREGAZIONE]
                                        ↑
                          +----------------------------+
                          | DB Transazionali (OLTP)   |
                          +----------------------------+
```

- **OLAP (On-Line Analytical Processing)**: motore di analisi multidimensionale sul Data Warehouse
- Il **[[data-warehouse]]** è la base dati direzionale, separata dai DB transazionali, ottimizzata per query analitiche

## Confronto SIO vs SID

| Caratteristica | SIO | SID |
|---|---|---|
| Livello Anthony | Operativo | Tattico / Strategico |
| Tipo elaborazione | OLTP | OLAP |
| Dati | Correnti, dettagliati | Storici, aggregati |
| Query | Semplici, frequenti | Complesse, occasionali |
| Utenti | Operatori | Manager, Dirigenti |
| Obiettivo | Eseguire operazioni | Supportare decisioni |

## Articoli correlati

- [[introduzione-si]]
- [[data-warehouse]]
- [[business-intelligence]]
- [[erp]]
- [[crm]]

## Fonti

- raw/L1-SI-INTRO.pdf
