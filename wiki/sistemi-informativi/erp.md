---
tags: [sistemi-informativi, ERP, SIO, enterprise-resource-planning, SAP, moduli]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L4-SI-ERP.pdf
---

# Sistemi ERP (Enterprise Resource Planning)

Un sistema ERP è una suite di moduli applicativi integrati che supportano l'intera gamma dei processi di un'impresa. L'acronimo ERP (coniato dal Gartner Group nei primi anni '90) significa: **E**nterprise = azienda/corporation; **R**esource = persone, materiali, impianti, capitale; **P**lanning = tempi, luoghi, quantità. È il principale esempio di [[tipologie-si|SIO]].

## Punti chiave

- Tre caratteristiche fondamentali del paradigma ERP: **unicità dell'informazione**, **estensione e modularità funzionale**, **prescrittività**.
- L'unicità si ottiene con una **base dati unica e condivisa** tra tutti i moduli (vs architettura ad isole dei sistemi legacy).
- La **prescrittività** costringe l'azienda ad adeguarsi alle best practice incorporate nel software (e viceversa tramite BPR).
- Un progetto ERP richiede una duplice analisi: i cambiamenti che l'azienda deve fare ([[bpr]]) e le parametrizzazioni del software.
- La suite si struttura su tre livelli: Suite → Modulo → Funzione.
- I moduli sono di tre tipi: **core settoriali** (attività primarie), **core intersettoriali** (supporto), **extended** (interazione con esterni: CRM, SCM).

## Il paradigma ERP

### 1. Unicità dell'informazione

**Il problema delle architetture ad isole (sistemi legacy):**
- Dati uguali duplicati in sistemi diversi
- Sincronizzazione tramite interfacce dedicate punto-a-punto
- Sfasamenti temporali e costi elevati su grandi dimensioni
- Elaborazione overnight spesso insufficiente a sincronizzare le isole

**La soluzione ERP:** base dati unica condivisa tra tutti i moduli. Vantaggi:
- **Sincronizzazione e non ridondanza**: l'arrivo di materiale a magazzino aggiorna automaticamente scorte, ordini fornitori e contabilità fornitori
- **Tracciabilità degli aggiornamenti**: ogni modifica alla base dati viene registrata, inclusi documenti cartacei/digitali associati
- **Integrazione dell'informazione direzionale**: i dati operativi unici sono facilmente aggregabili per il [[data-warehouse]] direzionale

```
Applicazioni supporto decisionale
        ↑
   DATA WAREHOUSE
        ↑
   Trasformazione info
        ↑
[ERP] DATABASE OPERATIVO
        ↑
Applicazioni supporto processi operativi
```

### 2. Estensione e modularità funzionale

La suite è un insieme di applicazioni software strutturate su tre livelli:
- **Suite (Livello 1)**: insieme completo di applicazioni che supporta tutti i processi gestionali
- **Modulo (Livello 2)**: applicazione che supporta uno specifico processo gestionale (es. gestione materiali); contiene funzioni software
- **Funzione (Livello 3)**: supporta un'attività elementare (es. ricevimento materiali); attivata da utenti via web/terminali

**Strategie di implementazione:**
- **Implementazione parziale**: piccolo gruppo di moduli che sostituisce i legacy (basso rischio)
- **One Stop Shopping**: tutti i moduli da un unico vendor (es. SAP)
- **Best of the Breed**: moduli migliori da vendor diversi (maggiore complessità di integrazione)

**Esempio moduli SAP R/3:**
- SD – Sales and Distribution
- MM – Materials Management
- PP – Production Planning
- QM – Quality Management
- PM – Plant Management
- HR – Human Resource
- FI – Finance
- CO – Controlling
- AM – Asset Management
- PS – Project System
- WF – Work Flow

### 3. Prescrittività

**Prescrittività**: normazione dei processi gestionali derivante dal modello funzionale incorporato nel software. Esempio: "i materiali che entrano in azienda devono essere stati ordinati".

- Il software norma il comportamento degli addetti
- L'impatto organizzativo può essere elevato: l'azienda si conforma allo standard ERP (best practice)
- Il punto chiave di un progetto ERP è la **gap analysis**: confronto tra il modello funzionale corrente (AS-IS) e le funzionalità ERP, per decidere se adeguare il processo al software o parametrizzare il software

| Attività | AS-IS | Funzionalità ERP | Azione |
|---|---|---|---|
| Ricevimento materiali | Solo registrazione | Controllo sull'ordine fornitore | Adeguamento processo |
| Controllo qualità | Regole predefinite | Solo registrazione esito | Adeguamento software |

## Struttura della suite: moduli settoriali e intersettoriali

| Tipo modulo | Funzione | Esempi |
|---|---|---|
| **Core settoriali** (verticali) | Attività primarie dell'azienda, specifici per settore | Pianif. materiali, Pianif. produzione, Ordini fornitore, Scheduling produzione |
| **Core intersettoriali** (orizzontali) | Attività di supporto, invarianti per settore | Gest. amministrativa, Gest. risorse umane, Gest. progetti, Pianificazione, Budgeting |
| **Extended** | Interazione con soggetti esterni (clienti, fornitori) | [[crm]] – Customer Relationship Management, SCM – Supply Chain Management, E-Procurement |

## Diffusione del mercato (2007)

| Vendor | Fatturato ($M) | Market Share |
|---|---|---|
| SAP | 11.753 | 41% |
| Oracle | 6.044 | 21% |
| Microsoft | 996 | 3% |
| Altri | 10.027 | 35% |

## Ciclo di vita di un progetto SAP (esempio reale)

Un progetto di implementazione SAP (standard leader ERP mondiale, fondato il 01/04/1972; acronimo = Systeme, Anwendungen, Produkte = System, Application and Products) segue fasi ben definite:

| Fase | Durata tipica | Contenuto |
|---|---|---|
| **Preparazione Progetto** | 3 mesi | Setup infrastruttura, definizione team, governance |
| **Business Blueprint** | 5 mesi | Analisi AS-IS, gap analysis, definizione TO-BE, gap analysis vs standard SAP |
| **Realizzazione** | 8 mesi | Configurazione, sviluppo customizzazioni, test unitari |
| **Finalizzazione** | 3 mesi | Test integrazione, formazione utenti, migrazione dati |
| **Go Live & Supporto** | 1 mese | Avvio in produzione, hypercare, stabilizzazione |

**SAP Business Suite moderna** (Cloud ERP):
- Core: Financial Management, Supply Chain Management, Human Capital Management, Customer Experience
- Esteso: Spend Management, Business Transformation Management
- AI: Joule Agents per orchestrazione end-to-end dei processi
- Fondazione: Business Technology Platform (BTP) + Business Data Cloud

## Articoli correlati

- [[tipologie-si]]
- [[bpr]]
- [[crm]]
- [[data-warehouse]]
- [[erp-evoluzione]]
- [[processi-aziendali]]

## Fonti

- raw/L4-SI-ERP.pdf
- raw/Elena_Andrini_UNIBS Inge 2025.pdf
