---
tags: [sistemi-informativi, ERP, industria-4.0, IoT, MES, digital-twin, CPPS]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L4-SI-ERP-Evoluzione.pdf
---

# Evoluzione degli ERP per l'Industria 4.0

L'Industria 4.0 è la trasformazione digitale dei processi produttivi e logistici delle industrie manifatturiere. Comporta un modello di produzione "agile" supportato da IoT, stampa 3D, cloud computing, dispositivi mobili, big data e AI. L'obiettivo è soddisfare domanda di prodotti **su misura a prezzi convenienti** con processi di massa altamente flessibili. In questo contesto, il sistema [[erp]] diventa la **spina dorsale** dell'insieme dei processi produttivi.

## Punti chiave

- Industria 4.0 = trasformazione digitale manifatturiera; tecnologie chiave: IoT, AI, Digital Twin, CPPS, Blockchain, Stampa 3D.
- Lo **Smart Object** (oggetto IoT connesso) abilita la rappresentazione digitale degli asset fisici.
- Il **CPPS (Cyber-Physical Production System)** integra risorse computazionali e processi fisici in un controllo a ciclo chiuso.
- Il **Digital Twin** è la rappresentazione virtuale aggiornata in tempo reale di un oggetto fisico tramite dati dei sensori.
- L'ERP deve integrarsi con il **MES (Manufacturing Execution System)** tramite integrazione verticale ed orizzontale per abilitare la produzione agile.
- Nuovo requisito ERP: storage ibrido (centralizzato + decentralizzato) per dati che non sono disponibili anticipatamente.

## Industria 4.0: tecnologie abilitanti

**Dalla fabbrica tradizionale alla Smart Factory:**

| Tecnologia | Ruolo |
|---|---|
| Sensori IoT | Monitoraggio real-time di macchinari e consumi |
| AI nel cloud | Analisi dati per manutenzione predittiva |
| MES/ERP integrati | Adattamento automatico della produzione agli ordini |
| Stampa 3D | Prototipi e piccole serie su misura |
| Robot collaborativi | Supporto agli operatori |
| Tracciabilità digitale | Lungo tutta la filiera |
| Blockchain | Tracciabilità e fiducia inter-organizzativa |
| Digital Twin | Simulazione e ottimizzazione processo |
| Augmented Reality | Supporto operatori in produzione |

**Risultato**: efficienza, flessibilità e personalizzazione a costi competitivi.

## IoT per produzione e logistica

L'IoT (Internet of Things) applicato all'industria rende "smart" gli oggetti fisici (macchine, attrezzi, pezzi di lavoro):
- Ogni oggetto fisico viene rappresentato digitalmente nel mondo digitale
- Gli **Smart Object** connessi formano un sistema per pianificazione, controllo ed esecuzione più flessibili

**Impatto:**
- **Cambiamenti nella filiera**: necessità di adattare modelli di business e servizi
- **Evoluzione dei processi**: obiettivo è migliorare processi esistenti o svilupparne di nuovi non realizzabili prima

## CPPS (Cyber-Physical Production Systems)

Un CPPS è un'integrazione di risorse computazionali e processi fisici:
- I computer integrati e connessi monitorano e controllano i processi fisici in un **controllo a ciclo chiuso**
- Il processo fisico e la logica computazionale si influenzano a vicenda
- Una rete di CPPS (CPPn) connette macchine lungo la linea di produzione

**Esempio**: in una linea produttiva, ogni macchina ha sensori e attuatori → monitoraggio costante → sistema centrale prevede guasti e ottimizza efficienza → per ogni macchina viene mantenuto un Digital Twin.

## Digital Twin

Un **Digital Twin** è una rappresentazione virtuale di un oggetto o sistema fisico, aggiornata in tempo reale usando dati provenienti da sensori.

**Applicazioni:**
- Simulare possibili modifiche alla linea di produzione e relativo impatto
- Prevedere potenzialmente guasti (manutenzione predittiva)
- Ottimizzare l'efficienza energetica

Permette di testare cambiamenti in ambiente virtuale prima di implementarli nel mondo reale, riducendo rischi e costi.

## Ruolo dell'ERP in Industria 4.0

L'ERP in Industria 4.0 deve:
- Collegare e far comunicare macchine intelligenti, sistemi logistici, impianti, sensori e dispositivi
- Adottare **modelli di processo e interfacce flessibili**
- Comunicare con i **MES (Manufacturing Execution Systems)** che conducono operazioni a livello di linea di produzione

## Integrazione ERP-MES

Il modello a tre livelli (standard ANSI/ISA S95):

```
ERP  ←→  Production Plan / General Production Information  ←→
MES  ←→  Production Schedule / Production Information      ←→
FACTORY FLOOR (Shop Floor / macchine)
```

- **ERP → MES**: invia il Production Plan
- **MES → ERP**: restituisce General Production Information
- **MES → Factory Floor**: invia Production Schedule
- **Factory Floor → MES**: restituisce Production Information

**Integrazione verticale**: collega livelli di pianificazione (ERP), controllo (MES) e esecuzione (Shop Floor) → consente tracciamento in tempo reale della trasformazione materie prime → prodotti finiti.

**Integrazione orizzontale**: connette lungo la supply chain i sistemi di supplier e customer via ERP.

## Nuovi requisiti per gli ERP: storage distribuito

L'ambiente Industry 4.0 introduce dati che non sono disponibili in anticipo (es. istruzioni di lavoro personalizzate per singolo pezzo). Soluzione: combinazione intelligente di archiviazione **centralizzata e decentralizzata**:
- Dati centrali nell'ERP
- Dati del pezzo di lavoro memorizzati decentralmente sul pezzo stesso (es. chip RFID)

**Esempio auto Industry 4.0:**
> Un'auto nella linea di produzione porta un chip RFID con tutte le specifiche di personalizzazione. Quando raggiunge una postazione, il chip invia un messaggio al MES che indirizza le macchine di conseguenza. Ogni passo viene registrato sul chip. Modifiche dell'ultimo minuto sono possibili in modo flessibile.

## Articoli correlati

- [[erp]]
- [[blockchain-si]]
- [[introduzione-si]]
- [[kpi-indicatori]]

## Fonti

- raw/L4-SI-ERP-Evoluzione.pdf
