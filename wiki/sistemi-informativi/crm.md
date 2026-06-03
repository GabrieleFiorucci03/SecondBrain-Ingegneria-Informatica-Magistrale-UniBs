---
tags: [sistemi-informativi, CRM, SIO, customer-relationship-management, canali, SFA]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L5-CRM.pdf
---

# Sistemi CRM (Customer Relationship Management)

I sistemi CRM supportano la costruzione di relazioni personalizzate di lungo periodo con i clienti, aumentandone la soddisfazione e, di conseguenza, il valore per il cliente e per l'impresa. Con il CRM, **il cliente diventa l'elemento centrale della strategia commerciale**: l'azienda confeziona offerte personalizzate e gestisce l'intero ciclo contatto-vendita-post-vendita. L'era dei CRM nasce nel 1993 con la suite di Tom Siebel.

## Punti chiave

- I CRM sono il **front-end** cliente-azienda; gli ERP (o i sistemi legacy) sono il **back-end** operativo; i SCM sono il secondo livello di back-end verso i fornitori.
- Tre pilastri del paradigma CRM: **multicanalità**, **unicità e completezza del dato cliente**, **catene di servizio end-to-end**.
- I canali del CRM sono: presenza/fisico (SFA), voce (call center), web (B2C e B2B), corrispondenza (FAX, SMS, email, chatbot).
- Il **CRM analitico** segmenta i clienti e guida le campagne di marketing tramite un data warehouse dedicato.
- Evoluzione recente: CRM 4.0 / CXM (Customer Experience Management), marketing automation, chatbot, CRM SaaS.

## Posizionamento del CRM nell'ecosistema applicativo

```
ERP/SCM (attività interne/fornitori) ←→ [BACK-END]
                                                ↕
                              Richieste / Feed-back
                                                ↕
CRM [FRONT-END] (processi di contatto + CRM analitico)
                                                ↕
              CLIENTE (canale presenza, voce, web, testo)
```

- **ERP (core)**: informatizza le attività interne
- **SCM**: informatizza le transazioni verso i fornitori
- **CRM**: informatizza l'intero ciclo vitale della relazione con il cliente (contatto → vendita → assistenza post-vendita)

## Il paradigma CRM

### Multicanalità
- Il cliente sceglie il canale più conveniente (presenza fisica, telefono, web, email/SMS)
- Strategia di business mirata a servire un cliente ubiquo 24 ore su 24
- Il CRM integra tutti i canali garantendo unicità del dato

### Unicità e completezza del dato cliente
- Ogni canale dispone di tutte le informazioni sul cliente e sui prodotti, indipendentemente dal canale o sistema che le ha create
- Realizzata integrando isole applicative separate con flussi dati e workflow coordinati

### Catene di servizio end-to-end
Il back-end CRM gestisce:
- **Provision**: esecuzione della richiesta
- **Delivery**: distribuzione del risultato
- **Administration**: gestione amministrativa connessa

## I canali del CRM

### Canale Presenza/Fisico
- Il cliente interagisce di persona con venditori dell'azienda (rete agenti, negozi, filiali)
- Il venditore è supportato da un sistema **SFA (Sales Force Automation)**:
  - Pianificazione e controllo attività
  - Monitoraggio opportunità e vendite effettive
  - Interazioni online con il cliente: pre-definizione profilo offerta, validazione interattiva
  - Applicazioni sia in B2C che B2B

### Canale Voce (Call Center / Contact Center)
- Numeri verdi e call center, diffusi dagli anni '90
- Funzioni: informazione e prenotazione, assistenza (help desk), punto di vendita
- Gestione **basi di dati clienti/prodotti/servizi**, registrazione ordini, guide operative per operatori (script)
- Campagne **inbound** (cliente chiama) e **outbound** (azienda chiama)

### Canale Web
**B2C (Business-to-Consumer)**:
- Vetrina: catalogo prodotti e offerte
- Transazione: carrello, pagamento, avanzamento ordine, log transazioni
- Post-vendita: assistenza, reclami, status ordini
- Proattività e profiling (suggerimento prodotti in base a comportamenti precedenti)
- **Clickstream analysis**: analisi dati di navigazione

**B2B (Business-to-Business)**:
- Clienti di alto valore, interazioni lunghe e basate su fiducia/contratti
- Supporta processo di vendita complesso (offerte, trattative, follow-up)
- Pipeline commerciali e opportunità di business
- Integrazione con ERP (ordini, fatture, assistenza)
- Portali clienti per documenti e ordini ricorrenti

### Canale Corrispondenza
- Testo scritto su supporti diversi: FAX, SMS, email
- Adatto a piccoli volumi, spesso complementare agli altri canali
- Applicazione di **text mining** per classificare richieste
- Tendenza recente: **chatbot** per risposta automatica alle richieste

## CRM Analitici

I CRM analitici si basano su un **data warehouse** con dati aggregati su clienti, transazioni e indici, e si dividono in:

**a) Sistema di analisi:**
- Calcolo di indici (acquisti, preferenze, comportamenti)
- Analisi e segmentazione clienti, report

**b) Sistema di azione:**
- Gestione campagne di marketing
- Gestione indici di valutazione cliente

### Tecniche di segmentazione clienti

| Criterio | Obiettivo |
|---|---|
| **Demografico** | Segmentare per località, istruzione, età, genere → marketing mirato |
| **Open Rates** | Segmentare per frequenza apertura email → follow-up e incentivi ai più interessati |
| **Storia degli Acquisti** | Identificare clienti alto valore e adattare le campagne |
| **Attività sul Sito Web** | Segmentare per comportamento online → campagne personalizzate |
| **Fase di Acquisto** | Adattare messaggio in base alla fase nel funnel di acquisto |

## Suite CRM: struttura completa

La suite CRM è composta da tre sottosistemi complementari:

| Sottosistema | Funzione | Input/Output |
|---|---|---|
| **CRM Operativo** | Informatizza le transazioni con il cliente lungo tutto il ciclo di vita | Fornisce dati al CRM Analitico |
| **CRM Analitico** | Informatizza la conoscenza sul cliente con tecniche di BI | Fornisce al CRM Operativo la conoscenza per interagire con i clienti |
| **CRM Direzionale** | Permette al management di pianificare e controllare la performance verso il cliente | Sistema di controllo di gestione orientato al cliente per misurare risultati e azioni correttive |

## Gestione campagne (fasi)

1. **Preparazione**: definizione scopo, risultati attesi, target clienti
2. **Progettazione**: definizione criteri target, offerte, canali, pianificazione
3. **Esecuzione**: estrazione target, contatto clienti
4. **Valutazione**: analisi risultati in termini di efficacia (costi) e comportamento clienti

## Evoluzione storica dei CRM

| Periodo | Tecnologia |
|---|---|
| Anni '70 | Sistemi di gestione degli ordini |
| Anni '80 | SFA – Sales Force Automation |
| Anni '90 | Numeri verdi e call center |
| 1995–2000 | Sistemi moderni con Web e Business Intelligence |
| 2010–oggi | CRM 4.0/CXM, marketing automation, chatbot, voicebot, CRM SaaS |

## Segmentazione RFM

Il modello **RFM** (Recency, Frequency, Monetary) è il criterio di segmentazione più diffuso nei CRM analitici:

| Dimensione | Significato | Utilizzo |
|---|---|---|
| **Recency** | Quanto recentemente il cliente ha acquistato | Clienti recenti → più propensi a riacquistare |
| **Frequency** | Quante volte ha acquistato nel periodo | Alta frequenza → clienti fedeli |
| **Monetary** | Quanto ha speso nel periodo | Alto valore monetario → clienti ad alto valore |

Le azioni CRM basate su RFM possono essere automatizzate tramite: email marketing mirate, notifiche personalizzate, raccomandazioni di prodotto.

## Articoli correlati

- [[erp]]
- [[tipologie-si]]
- [[data-warehouse]]
- [[business-intelligence]]
- [[kpi-indicatori]]

## Fonti

- raw/L5-CRM.pdf
