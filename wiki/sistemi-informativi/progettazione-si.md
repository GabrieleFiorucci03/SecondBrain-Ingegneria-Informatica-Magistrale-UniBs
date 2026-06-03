---
tags: [sistemi-informativi, progettazione, ciclo-di-vita, paradigmi, BPR, agile]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L2-SI-PROG.pdf
---

# Progettazione di Sistemi Informativi

La progettazione di un SI è il processo che porta dalla definizione dei requisiti all'implementazione e messa in operatività di un sistema informativo. Produce tre artefatti principali: architettura dei moduli software, organizzazione dei dati (Base di Dati), architettura hardware e software del sistema.

## Punti chiave

- Il ciclo di vita tradizionale è sequenziale: fattibilità → requisiti → progetto → implementazione → validazione → operatività.
- La progettazione si articola su due assi: **argomento** (Dati vs Processi) e **livello di astrazione** (Requisiti → Progetto concettuale → Implementazione).
- Esistono quattro paradigmi di progettazione: ex novo, assemblaggio pacchetti, BPR (ristrutturazione), agile/incrementale.
- Il **Business Process Reengineering (BPR)** è il paradigma per la ristrutturazione radicale di SI esistenti.
- Attività trasversali al ciclo di vita: documentazione, controllo qualità, configuration management.

## Ciclo di vita tradizionale

| Fase | Descrizione |
|---|---|
| **Studio di fattibilità** | Determinazione costi/benefici di diverse alternative; priorità tra componenti |
| **Raccolta e analisi dei requisiti** | Comprensione degli obiettivi ("missione"); interazione con utenti; requisiti in linguaggio naturale |
| **Progetto** | Specifica della struttura del SI; progetto di BD e applicazioni |
| **Prototipazione** | Implementazione semplificata per verifica; revisione/aggiunta di requisiti |
| **Implementazione** | Programmazione della versione operativa |
| **Validazione e testing** | Verifica che l'implementazione rispetti le specifiche di progetto |
| **Operatività** | Caricamento dati, esercizio, manutenzione, gestione e conduzione |

Fasi aggiuntive (trasversali o periodiche):
- **Pianificazione dei SI**: fase periodica di pianificazione progetti e attività di sviluppo
- **Assessment / Check-up**: verifica del funzionamento rispetto alle attese
- **Benchmarking**: confronto dei risultati con parametri di riferimento
- **Project management**: controllo trasversale di tutte le attività del ciclo di vita

## Segmenti di progettazione

La progettazione si struttura su una matrice Livello × Argomento:

|  | **Dati** | **Processi** |
|---|---|---|
| **Requisiti utente** | Classi/tipologie di dati, viste di utente | Attività aziendali, informazioni elaborate |
| **Progetto concettuale** | Entità, relazioni, attributi | Struttura processi, I/O processi, interazione |
| **Implementazione** | DBMS, schema logico, base dati | Linguaggi di programmazione, codifica programmi |

## Paradigmi di progettazione

### 1. Ex Novo
Sviluppo secondo il ciclo di vita tradizionale completo, partendo da zero.

### 2. Assemblaggio di pacchetti di mercato (plug-and-play)
Acquisto di pacchetti software standard (es. ERP) e loro personalizzazione per adattarli ai processi aziendali. Riduce tempi e costi ma richiede adattamento dei processi al software.

### 3. Ristrutturazione dell'esistente (BPR)
Ridisegno di un SI legacy (ereditato). Prevede reverse engineering del sistema attuale, analisi dei requisiti e riprogettazione. Vedi [[bpr]].

### 4. Approccio incrementale / Agile
Progettazione per rilasci successivi con evoluzione continua. Si accetta il cambiamento continuo dei requisiti. Adatto a contesti con requisiti incerti o in rapida evoluzione.

## Articoli correlati

- [[bpr]]
- [[introduzione-si]]
- [[tipologie-si]]
- [[erp]]

## Fonti

- raw/L2-SI-PROG.pdf
