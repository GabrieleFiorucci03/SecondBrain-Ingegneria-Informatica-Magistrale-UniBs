---
tags: [amministrazione-di-sistema, server, hardware, scalabilita, cluster, sistema-operativo]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - Server_COMPILED.pdf
---

# Server

Un **server** è un sistema (hardware + software) che eroga servizi e applicazioni ad altri sistemi (client). Il "sistema server" è uno stack a livelli — hardware, eventuale hypervisor, sistema operativo, servizi e applicazioni — affiancato da attività trasversali di supporto, manutenzione e consulenza. Per un server la regola fondamentale è che *deve funzionare*: l'amministratore deve decidere chi gestisce ciascun livello e con quali criteri sceglierlo e dimensionarlo.

## Punti chiave

- Le **caratteristiche** chiave di un server sono spesso riassunte con l'acronimo implicito RAS+: affidabilità (*Reliability*), stabilità (*Stability*), disponibilità (*Availability*), ripristinabilità (*Recoverability*), **scalabilità**, **prestazioni**, **manutenibilità** (*Manageability*) e **sicurezza**.
- Metriche di affidabilità: **MTBF** (Mean Time Between Failures) e **MTBC** (Mean Time Between Crashes).
- Form factor: **rack**, **tower**, **modulare** (blade), più formati embedded/mini, industriale, rugged, IoT.
- Soluzioni di acquisizione: **standard** (COTS, *Commercial Off-The-Shelf*), **custom** (es. piattaforma Google), **cloud** (IaaS).
- Dimensionamento: **scale-in** (scalabilità verticale, un server molto potente) vs **scale-out** (orizzontale, tanti piccoli server / sistemi distribuiti / web-scale-cloud-scale-hyper-scale). Esistono anche i **sistemi composable**.
- Affidabilità/prestazioni si incrementano con SMP multi-processore, controller RAID/storage enterprise, alimentatori ridondati e **cluster** (fisici o logici).

## Tipi di server e classificazione

I server si classificano: per **architettura**; per **sistema operativo**; per **servizi offerti** (File Server, Mail Server, DNS Server, Web Server, ...); per **risorse richieste** (CPU intensive, RAM intensive, Storage/I/O intensive). Tipicamente in un'organizzazione c'è più di un server per soddisfare esigenze di SO e servizi diversi, alta disponibilità e prestazioni adeguate. La **virtualizzazione** dei server è ormai mainstream (vedi [[virtualizzazione]]).

## Supporto, manutenzione e certificazioni

- **Supporto e garanzia** su hardware (es. NBD = Next Business Day, 4h, Prosupport) e software.
- **Certificazioni / supportabilità** tra i livelli: hardware↔SO, hardware↔hypervisor, hypervisor↔SO, applicazioni↔SO.
- **Contratti di manutenzione**: on-site, durata, tempistiche (es. intervento entro 2-4 ore); importanza della **manutenzione preventiva**.
- Server di marca vs assemblato: la marca offre affidabilità, contratti rapidi, disponibilità componenti nel tempo, certificazione hardware, console remotizzabile e design per funzionamento 24h×365g (flussi termici, hot-plug).

## Caratteristiche di un server

- **Affidabilità/stabilità/disponibilità/ripristinabilità**: il server e i suoi servizi devono funzionare.
- **Scalabilità**: adattare il server al carico di lavoro.
- **Prestazioni**: tempi di attesa non eccessivi.
- **Manutenibilità e semplicità**: configurazione e gestione semplici ma efficaci.
- **Sicurezza** (vedi [[sicurezza-di-sistema]]).
- Altro: disponibilità di driver, tipo di licenza e assistenza.

## Scelta e dimensionamento

La selezione di hardware e software deve soddisfare i requisiti di sistema server, privilegiando la **stabilità** (hardware e software consolidati). La **scelta hardware** riguarda CPU, RAM, storage e rete (vedi [[hardware-cpu-server]], [[memoria-ram-server]], [[storage-server]], [[bus-e-reti-server]]); la **scelta software** riguarda SO, applicazioni e servizi.

Criteri di scelta del software: affidabilità, compatibilità, funzionalità, supporto e manutenzione, diffusione, costi. Per **tipo di licenza**: software proprietario, software libero, software senza licenza.

### Cluster
- **Cluster fisico**: macchine in ridondanza tra loro, con SO distribuito o per cluster.
- **Cluster logico**: servizi ridondati tramite DNS o NAT.

## Sistemi operativi server

Ormai tutti i SO sono anche **NOS** (Network Operating System). Si classificano per funzionalità di rete (Client/Workstation vs Server) e per tipo di licenza (proprietario vs libero). Principali SO server: **Windows Server** (vedi [[server-windows]]), **Linux** (varie distribuzioni, vedi [[server-linux]]), **Unix** (macOS, FreeBSD, NetBSD, Solaris). Quota di mercato server storicamente divisa quasi alla pari tra Windows (~41%) e Linux (~41%). Il confronto Linux vs Windows tocca stabilità, velocità, sicurezza (numero/tipo di patch), semplicità, obsolescenza hardware e **TCO** (Total Cost of Ownership).

## Software sui server

- **Sistema operativo** (server).
- **Applicazioni tradizionali**: programmi di backup e di gestione.
- **Applicazioni client-server**: servizi (demoni), lato server di applicazioni note.
- **Applicazioni web-oriented**.

## Chi lavora sui server

L'**amministratore di sistema** (vedi [[figura-amministratore-di-sistema]]), persona/e fisica interna o azienda esterna. Rapporto tipico server:sistemisti di 20-30:1; per ogni dollaro speso in hardware ne servono circa 8 di gestione (caso server fisici). Oltre a lui: tecnici, programmatori, utenti, analisti.

## Efficienza energetica e termica

Consumo di un server moderno ripartito tra CPU, memoria e altri componenti. Trend: **fresh air cooling** (escursioni fino a 45°C), **performance per Watt**, **DCIE** (Data Center Infrastructure Efficiency), **SNIA Emerald** (standard ISO/IEC 24091:2019 per la power efficiency dello storage in data center).

## Articoli correlati

- [[figura-amministratore-di-sistema]]
- [[hardware-cpu-server]]
- [[memoria-ram-server]]
- [[storage-server]]
- [[bus-e-reti-server]]
- [[server-linux]]
- [[server-windows]]
- [[virtualizzazione]]

## Fonti

- `raw/2 - Server_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server" (concetto di sistema server, tipi, caratteristiche, scelta e dimensionamento, SO server).
