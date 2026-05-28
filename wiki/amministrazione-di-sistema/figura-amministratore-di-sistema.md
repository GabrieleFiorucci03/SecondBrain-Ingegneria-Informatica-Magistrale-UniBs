---
tags: [amministrazione-di-sistema, sistemista, figure-professionali, gdpr, introduzione]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/1 - Introduzione_COMPILED.pdf
---

# Figura dell'amministratore di sistema

L'**amministratore di sistema** (*System Administrator*, spesso detto *sistemista*) è il professionista responsabile della gestione, manutenzione e ottimizzazione dei sistemi informatici e delle infrastrutture tecnologiche di un'organizzazione. È la figura che garantisce che le risorse informatiche siano efficienti, sicure e disponibili per utenti e applicazioni. In Italia il termine "sistemista" è usato in modo generico e spesso impreciso.

## Punti chiave

- Il *System Administrator* gestisce sistemi e reti per conto di un datore di lavoro o cliente; lavora nel reparto IT (interno o in outsourcing) ed è tipicamente "chi risolve i problemi".
- Le figure di amministrazione si articolano in profili specifici: **System Administrator**, **Network Administrator**, **Storage Administrator**, **Security Administrator**, **Database Administrator (DBA)**, **Virtual System Administrator**, più i nuovi ruoli legati al *cloud*.
- I profili sono normati in Italia dalle norme **UNI-11506** e **UNI-11621-2**, che distinguono anche i *Technical Specialist* (specialisti hardware/software altamente specializzati).
- I compiti si dividono in **fase iniziale** (dimensionamento, installazione, configurazione) e **run-time** (avvio/arresto, gestione hardware/utenti/filesystem/processi, log, backup, sicurezza, monitoraggio, ottimizzazione).
- La salvaguardia dei dati ha implicazioni con il **GDPR**: il provvedimento del Garante del 27 novembre 2008 definisce formalmente la figura dell'amministratore di sistema.
- Requisiti chiave: competenze tecniche ampie (hardware, software, reti, sicurezza), esperienza sul campo, capacità di apprendimento continuo, problem-solving/troubleshooting e capacità progettuali.

## Tipi di amministratori

La macro-suddivisione delle figure informatiche separa il **software** (sviluppatore, progettista, analista) dai **sistemi di elaborazione** (sistemista, net manager); tra i due estremi esiste un continuum di figure professionali. Le tipologie di "amministratore" includono:

- **System Administrator** — amministratore di sistema (Unix/Linux, Windows, ...).
- **Virtual System Administrator** — gestione di sistemi virtualizzati.
- **Network Administrator** — amministratore di rete; opera su router, switch, cablaggio, firewall e accordi con i fornitori di connettività.
- **Storage Administrator** — gestione dello storage.
- **Security Administrator** — amministratore della sicurezza.
- **Database Administrator (DBA)** — amministra i DBMS, ne ottimizza prestazioni e affidabilità, realizza reportistica e integrazione tra basi dati diverse.
- Nuovi ruoli legati al mondo **cloud**.

## Definizione formale e normativa

Un *System Administrator* è definito come «*one who, as a primary job function, manages computer and network systems on behalf of another, such as an employer or client*». Le norme **UNI-11506** e **UNI-11621-2** dettagliano i profili: il System Administrator opera su hardware, sistemi operativi, gestione di credenziali e diritti di accesso, svolgendo installazione, configurazione, monitoraggio e risoluzione di **incidenti** (interruzioni non programmate) e **problemi** (le cause dei malfunzionamenti). I **Technical Specialist** sono tecnici altamente specializzati su uno specifico prodotto hardware o software, con percorsi di formazione dedicati.

## Compiti dell'amministratore

I compiti variano a seconda della dimensione aziendale: in aziende piccole può esistere un'unica figura (anche esterna); in aziende grandi esistono team dedicati per ciascun ruolo.

**Amministratore di sistema:**
- *Fase iniziale*: dimensionamento e scelta dei sistemi, installazione, configurazione di sistemi e servizi.
- *Run-time*: avvio/arresto dei sistemi, gestione di hardware, utenti, filesystem, processi/servizi/applicazioni, log e backup, sicurezza, monitoraggio e check, ottimizzazione.

**Amministratore di rete:**
- *Fase iniziale*: progettazione della rete, scelta dei dispositivi (server, internetworking, cablaggio), configurazione (reti logiche/fisiche, assegnazione IP, routing).
- *Run-time*: gestione dei server e dei servizi di rete (account centralizzati, filesystem remoti, web), sicurezza (firewall, proxy, NIDS), monitoraggio.

Un compito trasversale è la **salvaguardia dei dati**, con implicazioni GDPR che coinvolgono il titolare del trattamento, il responsabile del trattamento e il **Data Protection Officer (DPO)**.

## Attività reali e organizzazione

Il buon sistemista partecipa alle riunioni di definizione dell'infrastruttura, installa le macchine, esegue procedure standard di post-installazione (aggiornamenti, rimozione servizi inutili, **hardening**, script custom), installa/configura/testa i servizi, monitora performance e sicurezza, assiste gli utenti, definisce ed esegue le politiche di backup (compresi i **test di ripristino**), automatizza le procedure e documenta il lavoro.

Tra le attività emergono operazioni "shallow" (configurazione applicazioni, provisioning di VM/container, patch, clustering, monitoraggio) e "deep" (aggiornamenti BIOS/firmware, gestione remota dell'alimentazione, decommissioning). L'organizzazione si fonda su documentazione accurata (scheda per ogni sistema, "giornale di bordo"), strumenti adeguati (monitoraggio, gestione, deployment) e **best practices** consolidate.

## Requisiti e percorso formativo

Un sistemista necessita di: capacità tecniche su più argomenti (hardware, software, reti, sicurezza), esperienza (aula, laboratorio, campo), capacità di apprendere e restare aggiornato, una "borsa degli attrezzi", e forti capacità di **problem-solving** e **troubleshooting**. Le tecniche di troubleshooting comuni includono: approccio a livelli, ad esclusione, bottom-up, top-down, per assurdo; più tecniche analitiche (log file, dump dei pacchetti) e proattive.

Il percorso formativo combina conoscenza teorica (SO, reti, calcolatori), conoscenza pratica ed esperienza sul campo (*training on the job*). Il modello **Dreyfus** di acquisizione delle competenze (Stuart e Hubert Dreyfus, 1980) prevede 5 livelli: Novice, Advanced Beginner, Competent, Proficient, Expert.

## Principi e cultura del mestiere

Tra le "regole non scritte": *experience is more important than knowledge*, *simple is better than complex*, *best practices are never absolutes*, *(usually) it depends*. Sono noti i "Ten Commandments of System Administration" (backup regolari e completi, fiducia nei propri server, essere i primi a sapere quando qualcosa va giù, mantenere log su tutto, documentare policy e procedure, usare cifratura per i servizi insicuri, non perdere i log quando un server muore, automatizzare i task ripetitivi) e i comandamenti di sicurezza per ogni SysAdmin. Il **System Administrator Appreciation Day** si celebra l'ultimo venerdì di luglio.

## Articoli correlati

- [[server]]
- [[server-linux]]
- [[server-windows]]
- [[sicurezza-di-sistema]]

## Fonti

- `raw/1 - Introduzione_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo introduttivo sulla figura dell'amministratore di sistema.
