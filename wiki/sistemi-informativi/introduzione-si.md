---
tags: [sistemi-informativi, organizzazione, modelli, IT, anthony]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L1-SI-INTRO.pdf
---

# Introduzione ai Sistemi Informativi

Un Sistema Informativo (SI) è un insieme di diversi componenti che consentono la produzione e la gestione dell'informazione. I SI sostengono i processi operativi, decisionali e strategici di un'organizzazione, connettendo tecnologia e organizzazione e costituendo la base della digitalizzazione di imprese e istituzioni.

## Punti chiave

- Un SI si modella su tre livelli: **organizzativo** (uso e utilità), **funzionale** (cosa deve fare), **informatico** (come è implementato).
- L'azienda è un insieme di processi; le informazioni devono circolare nella giusta misura tra tutti i livelli.
- Il successo o l'insuccesso di un'azienda deriva dalla qualità della gestione dell'informazione.
- L'IT impatta su **efficienza** (Output/Input: costo del raggiungimento degli obiettivi) ed **efficacia** (Output effettivo/Output atteso: grado di raggiungimento degli obiettivi).
- I processi aziendali si dividono in tre livelli secondo la **Piramide di Anthony**: strategico, tattico, operativo.
- Le due macro-tipologie di SI aziendali sono [[tipologie-si|SIO (Operativi) e SID (Direzionali)]].

## Il modello organizzativo

Descrive l'uso e l'utilità di un SI all'interno di un'azienda. Il riferimento è il modello di **Anthony**, che classifica i processi aziendali in tre categorie:

| Livello | Descrizione | Esigenze informative |
|---|---|---|
| **Strategico** | Scelta obiettivi, individuazione risorse, definizione politiche | Macrofenomeni e tendenze, niente dettagli |
| **Tattico (controllo)** | Programmazione risorse, controllo raggiungimento obiettivi | Segnalazione anomalie rispetto ai programmi |
| **Operativo** | Svolgimento attività correnti | Sistema real-time per routine |

**Esempio azienda manifatturiera:**
- Operativo: produzione, gestione e controllo commesse
- Tattico: allocazione personale a sedi, controllo scostamenti preventivo/consuntivo
- Strategico: scelta delle aree di mercato più convenienti

## Il modello funzionale

Descrive le esigenze di elaborazione dell'informazione ("cosa" il SI deve fare, a prescindere dal "come"). Integra tre prospettive:

- **Modello dei casi d'uso**: definisce le interazioni utente-sistema
- **Modello del flusso di attività**: descrive il flusso delle attività che il sistema supporta
- **Modello delle informazioni**: definisce struttura e contenuti della base di dati

I modelli funzionali sono espressi tramite notazioni formali, in particolare **UML**.

## Il modello informatico

Descrive come i SI sono implementati. Si articola su due livelli:

### Modello applicativo (tre strati)
- **Strato dei dati**: struttura le basi di dati (raccolte permanenti organizzate secondo uno schema)
- **Strato delle regole**: logica di elaborazione (realizzata con vari linguaggi di programmazione)
- **Strato di presentazione**: GUI (Graphical User Interface) per il dialogo con l'utente

### Modello tecnologico (architettura di elaborazione)
Basato sul paradigma **client-server** (comunicazioni tramite scambio di messaggi):

| Architettura | Componenti |
|---|---|
| 2-tier | Client + Server |
| 3-tier | Presentation Server + Application Server + Database Server |
| 4-tier | Browser + Web Server + Application Server + Database Server |

La scalabilità aumenta con il numero di livelli.

## Tecnologie IT

Quattro categorie:

- **Automazione**: parte del processo produttivo (robot, bancomat, macchine controllo processo)
- **Supporto alle decisioni**: gestione del processo produttivo (contabilità, e-mail, sistemi direzionali)
- **Embedded**: incorporate nei prodotti/servizi forniti (strumentazione auto, Internet Banking)
- **Infrastrutturali**: inter-organizzative per scambi tra organizzazioni (Internet, 5G, WiFi)

**Efficacia IT = f(architettura hw+rete, architettura software, architettura dati, prestazioni & sicurezza, fattori di contesto)**

## Ruoli dell'IT

L'IT rappresenta:
- Un'**opportunità** per migliorare le prestazioni dei processi
- Un **fattore di costo** rilevante (fino al 50% dei costi di progetto)
- Un **fattore di fattibilità** della strategia di innovazione (vincoli economici, organizzativi, tecnologici)

## Funzioni di un Sistema Informativo

1. Raccolta e acquisizione delle informazioni
2. Archiviazione e conservazione delle informazioni
3. Elaborazione delle informazioni
4. Distribuzione e scambio di informazioni

## Articoli correlati

- [[tipologie-si]]
- [[erp]]
- [[crm]]
- [[business-intelligence]]
- [[kpi-indicatori]]
- [[data-warehouse]]

## Fonti

- raw/L1-SI-INTRO.pdf
