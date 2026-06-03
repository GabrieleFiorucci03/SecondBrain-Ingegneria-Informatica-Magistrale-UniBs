---
tags: [sistemi-informativi, processi-aziendali, catena-del-valore, Porter, enterprise-systems]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/SI - Teoria Primo Parziale.pdf
  - raw/Elena_Andrini_UNIBS Inge 2025.pdf
---

# Processi Aziendali e Enterprise Systems

Un **processo aziendale** è un insieme di attività e decisioni finalizzato alla creazione di un output al quale il cliente attribuisce un valore misurabile. Le attività partono da un cliente (esterno o interno) e includono attività front-end, back-end e consegna. L'architettura dei processi definisce come SI e [[erp|Enterprise Systems]] si mappano sui processi di un'organizzazione.

## Punti chiave

- I processi si dividono in: **primari** (costruire il prodotto/servizio), **di supporto** (fornire risorse ai processi primari), **di direzione e controllo** (comunicare obiettivi e vincoli).
- **Ciclo attivo**: operazioni con il cliente → guadagno economico-finanziario.
- **Ciclo passivo**: processi e workflow con fornitori e magazzino.
- La **Catena del Valore di Porter** descrive processi primari e di supporto per settore: manifatturiero, servizi, time-to-market.
- **Enterprise Systems orizzontali** (supporto, invarianti per settore) vs **verticali** (primari, specifici per settore).
- Il **Modello del Portafoglio Applicativo** mappa gli ES sulle fasi della catena del valore × pianificazione/esecuzione.

## Architettura dei processi

```
              "MERCATO"
         Domanda ↓  ↑ Prodotti/Servizi
    ┌─────────────────────────────────┐
    │ Processi     │ PROCESSI       │ Processi    │
    │ Direzione   │ PRIMARI        │ di Supporto │
    │ e Controllo │ (front-end     │ (risorse)   │
    │             │  back-end      │             │
    │  Pianific.  │  consegna)     │  HR         │
    │  Controllo  │                │  Finanziarie│
    │  gestione   │                │  Tecnologie │
    └─────────────────────────────────┘
```

### Ciclo attivo vs ciclo passivo

| Ciclo | Contenuto | Esempi |
|---|---|---|
| **Ciclo attivo** | Operazioni che si intrattengono con il cliente, generano guadagno economico | Ordini clienti, vendite, fatturazione |
| **Ciclo passivo** | Processi e workflow con fornitori e magazzino | Ordini acquisto, ricevimento merci, pagamenti fornitori |

## Catena del Valore di Porter

### Azienda manifatturiera

```
[Fornitori] → Gestione MP → Trasformazione → MKTG & Vendita → Distribuzione → Post-Vendita
                              ←————————— Buy side ——————————————— Sell side ———→
    ─────────────────── Processi di supporto ─────────────────────────────
    Approvvigionamento | Sviluppo Tecnologie | Gestione Risorse | Infrastrutturali
```

| Processo primario | Descrizione |
|---|---|
| **Gestione materie prime** | Rapporto con fornitori, approvvigionamento materie prime |
| **Trasformazione** | Produzione: input (MP, semilavorati) → output (prodotto con valore aggiunto) |
| **Marketing e vendita** | Piazzare sul mercato e raccogliere ordini |
| **Distribuzione** | Logistica: raccogliere ordini e consegnare ai clienti |
| **Post-vendita** | Assistenza al cliente in caso di guasti |

### Azienda di servizi

```
Processi primari: Predisposizione condizioni produttive → Back Office → Front Office → Procacciamento clientela
Processi di supporto: Infrastrutturali | Gestione risorse umane | Sviluppo tecnologia | Comunicazione interna/esterna
```

**Nota**: I processi di supporto sono circa sempre uguali al variare del settore. I processi primari cambiano drasticamente da settore a settore.

### Azienda time-to-market

Per mercati basati su idee innovative, prima ancora di sapere se ci sono clienti:
```
Identificare mercato → Progettare prodotto/servizio → Realizzare → Consegnare → Assistere
        ←— Processo di innovazione —→  ←——— Processo operativo ———→  ←— Post vendita —→
```

## Enterprise Systems: orizzontali vs verticali

| Tipo ES | Caratteristiche | Esempi |
|---|---|---|
| **Orizzontali** | Gestiscono processi di supporto; non variano con il settore merceologico | HR, Finance, Contabilità, Acquisti |
| **Verticali** | Gestiscono processi primari; specifici per settore | Gestione produzione manifatturiera, Gestione sinistri (assicurazioni) |

**Mappa strategica degli ES**: capire quanti processi aziendali sono coperti e supportati adeguatamente dal SI.

## Modello del Portafoglio Applicativo

Metodo per individuare e classificare gli Enterprise Systems di un'impresa manifatturiera, incrociando:
- Le **fasi della catena del valore** (Gestione MP, Trasformazione, MKTG, Distribuzione, Post-vendita, Supporto)
- Le **attività operative** richieste per ogni fase: **Pianificazione Operativa** e **Esecuzione**

Produce una mappa dei processi con i SW associati → evidenzia gap di copertura.

## Articoli correlati

- [[erp]]
- [[crm]]
- [[introduzione-si]]
- [[bpr]]
- [[progettazione-si]]

## Fonti

- raw/SI - Teoria Primo Parziale.pdf
- raw/Elena_Andrini_UNIBS Inge 2025.pdf
