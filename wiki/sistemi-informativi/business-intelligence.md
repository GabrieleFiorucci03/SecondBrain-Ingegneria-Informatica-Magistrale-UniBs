---
tags: [sistemi-informativi, business-intelligence, BI, big-data, semantic-layer, ETL, data-mining]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/9-SIA-BI-2025.pdf
---

# Business Intelligence

La Business Intelligence (BI) è il processo di elaborazione che **trasforma i dati in informazioni** a supporto del prendere **decisioni**, realizzato tramite un insieme di tecnologie (Grothe e Gentsch, 2000; Davenport e Prusak, 1998). Il percorso è: **Dati → Informazioni → Conoscenza**. Le organizzazioni raccolgono sempre più dati ma hanno difficoltà a ricavarne valore: la BI è la risposta a questo divario.

## Punti chiave

- **Dati**: nozioni grezze e incomplete; **Informazioni**: dati elaborati con valore aggiunto; **Conoscenza**: identificazione di relazioni causa-effetto attraverso l'esperienza.
- I Big Data sono caratterizzati dalle **3V**: Volume (crescita esponenziale), Variety (formati eterogenei), Velocity (velocità di generazione e analisi).
- L'architettura BI moderna: Sorgenti → ETL/ELT → [[data-warehouse|DWH]] → Semantic Layer → BI Tools.
- Il **Semantic Layer** è lo strato logico che astrae la complessità tecnica e standardizza [[kpi-indicatori|KPI]] per tutta l'azienda.
- La BI è caratterizzata da: **ampiezza** (integra dati da tutta l'azienda), **profondità** (raggiunge tutti i livelli), **completezza** (piattaforma end-to-end), **previsionalità** (analisi predittive e ML).
- Il ciclo virtuoso BI: Problema → Diagnosi → Conoscenza → Azione → Risultati → Problema.

## Il problema: organizzazioni sommerse dai dati

Le aziende raccolgono più dati che mai da: ERP Transactions, CRM Data, Web Click Streams, Social Media, IoT, Location Tracking, Purchase Data, Credit Histories, Activity Records. Ma la **capacità di collezionare e memorizzare dati ha superato la capacità di dare loro un senso**.

### Le 3V dei Big Data

| Dimensione | Problema | Impatto BI |
|---|---|---|
| **Volume** | Crescita esponenziale (ERP, CRM, IoT, log, social) | Supera le capacità degli strumenti tradizionali |
| **Variety** | Non solo tabelle: testi, immagini, eventi, API, streaming | Serve integrare formati eterogenei |
| **Velocity** | Mercati rapidi, decisioni in tempo reale | BI statica non basta; servono dashboard live e alert |

Aggiunta dai docenti: **ML & AI** come quarta sfida — domande moderne (churn, forecasting, cause dei KPI) richiedono capacità analitiche che gli strumenti classici non supportano.

## Definizione di Dati, Informazioni, Conoscenza

```
DATI (grezzi, incompleti)
   ↓ analisi, sintesi, contestualizzazione, calcolo, categorizzazione
INFORMAZIONI (dati con valore aggiunto)
   ↓ trasferimento nell'organizzazione, relazioni causa-effetto
CONOSCENZA AZIENDALE
```

## Perché la BI è cruciale

| Livello decisionale | Uso della BI |
|---|---|
| **Strategico** | Definizione obiettivi, investimenti, direzione aziendale basata su dati affidabili |
| **Tattico** | Pianificazione commerciale, marketing, pricing, allocazione risorse |
| **Operativo** | Monitoraggio giornaliero di KPI, performance processi, anomalie, inefficienze |
| **Trasversale** | Allineamento dell'organizzazione ai dati: versione unica e condivisa della realtà |

La BI non è solo per i livelli superiori della gerarchia: la ricerca intelligente di dati è fondamentale per produttività ed efficienza a **tutti i livelli** (dalla direzione all'operatore di macchina utensile).

## Architettura BI moderna

```
SORGENTI (ERP, CRM, file, database, IoT, social)
          ↓
       ETL / ELT
          ↓
   DATA WAREHOUSE (DWH)
          ↓
    SEMANTIC LAYER
          ↓
      BI TOOLS
   (Self-service | Dashboard & Apps | Reporting)
          ↓
      UTENTI (User | Analyst | Developer IT)
```

- **ETL/ELT**: processo di Estrazione, Trasformazione (o Load-then-Transform), Caricamento
- **DWH**: storage analitico (vedi [[data-warehouse]])
- **Semantic Layer**: strato di astrazione business
- **BI Tools**: strumenti di visualizzazione, analisi e reporting

**Esempio SAP BusinessObjects BI Suite:**
Sorgenti (SAP ERP, altri DW, OLAP Cubes, Excel, Social Media) → Universe Semantic Layer → BI Platform → Self-service / Dashboards / Reporting → Utenti via Mobile, web portal, MS Office, cloud.

## Il Semantic Layer

È uno **strato logico** che astrae la complessità tecnica (tabelle, join, SQL) e permette agli utenti business di lavorare su concetti familiari.

### Funzioni principali

**1. Astrazione tecnica**
- Gli utenti non vedono `F_Sales`, `product_id`, `net_revenue`
- Vedono: Vendite, Cliente, Margine, Prodotto, Periodo

**2. Standardizzazione KPI**
- Definisce metriche, KPI, logiche di calcolo, aggregazioni, filtri e relazioni
- Tutta l'azienda usa gli stessi KPI definiti e computati allo stesso modo
- **Problema senza Semantic Layer**: tre reparti (Vendite, Finance, Controlling) calcolano il "Margine Commerciale" in 3 modi diversi → report incoerenti → decisioni sbagliate
- **Soluzione**: Margine Commerciale = Ricavi Netti − Costi Diretti (unica versione ufficiale)

**3. Governance e sicurezza**
- Controlla chi può vedere quali dati
- Definisce quali campi sono sensibili e quali metriche sono certificate
- Audit e lineage completo di come una metrica viene calcolata

**4. Relazioni e gerarchie**
- Relazioni tra tabelle: Customer → Orders → Products
- Gerarchie: Anno > Mese > Giorno
- Dimensioni: Cliente, Tempo, Prodotto

## Aspetti paradigmatici della BI

| Aspetto | Descrizione |
|---|---|
| **Ampiezza** | Integra dati, funzioni e tecnologie da tutti i comparti aziendali |
| **Profondità** | Raggiunge tutti i dipendenti con strumenti adatti al loro ruolo specifico |
| **Completezza** | Piattaforma integrata end-to-end con dati comuni e rappresentazione coerente |
| **Previsionalità** | Non solo osservazione dell'attuale: previsioni con tecniche analitiche innovative (ML) |

## Il ciclo virtuoso della BI

```
Problema → [Identificare problema e opportunità]
              ↓
           Diagnosi → [KDD / Data Mining]
              ↓
           Conoscenza → [Utilizzare la conoscenza → azione]
              ↓
           Risultati → [Misurare gli effetti dell'azione]
              ↓
          (feedback al) Problema
```

Il **KDD (Knowledge Discovery in Databases)** alimenta il ciclo: Data Consolidation → Selection & Preprocessing → Data Mining → Pattern & Models → Interpretation & Evaluation → Knowledge.

## BI e Data Mining

Dall'architettura BI completa:
- **Query / OLAP**: Business Queries, Multi Dimensional Analysis
- **Data Mining**: Customer Segmentation, Basket Analysis, Fraud Detection, Target Marketing

## Articoli correlati

- [[data-warehouse]]
- [[tipologie-si]]
- [[kpi-indicatori]]
- [[erp]]
- [[crm]]

## Fonti

- raw/9-SIA-BI-2025.pdf
