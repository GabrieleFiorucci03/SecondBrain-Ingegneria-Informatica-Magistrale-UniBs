---
tags: [backup, bcdr, disaster-recovery, business-continuity, rpo, rto, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/5 - SoluzioniEnterprise.pdf
---

# BCDR e Backup

**BCDR (Business Continuity & Disaster Recovery)** è l'insieme delle strategie e tecnologie che permettono a un'organizzazione di continuare ad operare o di ripristinare le operazioni a seguito di eventi avversi — guasti hardware, errori umani, disastri naturali, attacchi informatici.

## Punti chiave

- **Business Continuity (BC)**: capacità di continuare il business nonostante eventi avversi; include HA, SC, DR e COOP.
- **RPO** (Recovery Point Objective): quanti dati possiamo perdere — definisce la frequenza di backup.
- **RTO** (Recovery Time Objective): quanto tempo ci vuole per ripristinare — definisce le soluzioni tecnologiche.
- **Regola 3-2-1-1-0**: 3 copie, 2 media diversi, 1 offsite, 1 offline/immutabile, 0 errori verificati.
- **Disaster Recovery ≠ Cyber Recovery**: il DR classico non protegge da attacchi ransomware che cifrano anche le repliche; serve isolamento (air-gap/vault).
- **Backup ≠ Snapshot ≠ Replica**: snapshot e repliche non sostituiscono il backup; possono essere usate come restore point aggiuntivi.

---

## Tipi di downtime

| Categoria | % Occorrenze | Esempi |
|-----------|-------------|--------|
| **Disastri** | <1% | Alluvioni, incendi, terremoti, contaminazione edificio |
| **Downtime non pianificato** | ~13% | Guasto HW, corruzione DB, errore umano, virus |
| **Downtime pianificato** | ~87% | Backup, manutenzione, aggiornamenti, batch processing |

**Cause perdita dati**: Hardware Failure (40%), Human Error (29%), Software Corruption (13%), Computer Viruses (6%), Theft (9%), Hardware Destruction (3%).

**Rischio Italia**: 82% comuni a rischio idrogeologico; terremoti (5.000 comuni), eruzioni (100 comuni), frane/alluvioni (6.000 comuni).

---

## Metriche BCDR

| Metrica | Definizione | Impatto |
|---------|-------------|---------|
| **RPO** (Recovery Point Objective) | Massima quantità di dati perdibili (misura quanto indietro nel tempo si può andare) | Determina frequenza e tipo di backup |
| **RTO** (Recovery Time Objective) | Massimo tempo tollerabile per il ripristino del servizio | Determina le tecnologie di recovery |
| **WRT** (Work Recovery Time) | Tempo per recuperare dati persi e backlog dopo il ripristino del sistema | Spesso ignorato nella pianificazione |
| **MTD** (Maximum Tolerable Downtime) | RTO + WRT; limite assoluto oltre il quale il business è compromesso | Vincolo di business, non tecnico |
| **MTBF** | Mean Time Between Failures | Affidabilità del componente |
| **MTTR** | Mean Time To Repair | Efficienza del team operativo |
| **SLA** | Service Level Agreement; contratto sul livello di servizio | Vincolo contrattuale/legale |

**Formula disponibilità**: `A = MTBF / (MTBF + MTTR)`

**RPO vs tecnologia**:
- Settimane: Tape Backup
- Giorni: Replica Periodica
- Ore: Replica Asincrona
- Secondi: Replica Sincrona / Global Cluster

---

## Tier BCDR

| Tier | RPO | RTO | Costo | Soluzione |
|------|-----|-----|-------|----------|
| I | Immediato | Immediato | $$$ | Cluster HA + storage sincrono |
| II | >24 ore | >48 ore | $$ | Replica asincrona + DR site |
| III | >7 giorni | >5 giorni | $ | Backup su nastro + site remoto |

---

## Linee di difesa BCDR

| Linea | Tipo | Obiettivo |
|-------|------|-----------|
| **1ª** | HA (High Availability) | Eliminare SPOF; hardware ridondato + cluster |
| **1ª–2ª** | SC (Service Continuity) | Continuità del servizio senza interruzioni |
| **1ª–2ª** | DR (Disaster Recovery) | Ripristino servizi/dati dopo failure, deletion, corruption |
| **3ª** | COOP (Continuation of Operations) | Continuità in caso di disastro; CED remoto |

---

## Disaster Recovery vs Cyber Recovery

| Categoria | Disaster Recovery | Cyber Recovery |
|-----------|-----------------|----------------|
| Recovery Time | Quasi immediato | Affidabile e veloce (~1 giorno) |
| Recovery Point | Continuo ideale | ~1 giorno medio |
| Natura disastro | Alluvione, blackout, guasto | Attacco mirato, ransomware |
| Diffusione | Regionale, contenuta | Globale, si propaga velocemente |
| Topologia | Connesso al sito primario | **Isolato** (air-gap o vault) |
| Volume dati | Tutti i dati | Selettivo (servizi fondamentali) |
| Recovery | Standard DR (failback) | Iterativo, selettivo |

**Cyber Recovery Vault**: soluzione isolata (non connessa alla rete produzione) con dati immutabili, analisi di integrità e recovery orchestrato. Il 93% degli attacchi ransomware tenta di distruggere anche i backup.

---

## Backup — concetti fondamentali

### Metodi di backup

| Metodo | Cosa salva | Tempo backup | Spazio | Ripristino |
|--------|-----------|-------------|--------|-----------|
| **Full** | Tutto | Lungo | Molto | Semplice (1 file) |
| **Differenziale** | Modifiche dall'ultimo full | Medio (cresce) | Medio (cresce) | Full + 1 diff |
| **Incrementale** | Modifiche dall'ultimo backup (qualsiasi) | Breve | Minimo | Full + tutti gli incr. |
| **Incrementale inverso** | Mantiene sempre il backup più recente come full | — | Variabile | 1 file (più recente) |
| **Full sintetico** | Unisce full + incrementali senza rileggere la sorgente | Solo incrementale delta | Variabile | 1 file |

### Consistenza dei dati

| Livello | Descrizione | Rischio |
|---------|-------------|---------|
| **Crash-consistent** | Snapshot del disco "as-is" | DB corrotto, transazioni incomplete |
| **File-system consistent** | Flush I/O pendenti prima dello snapshot | OK per file; DB può essere inconsistente |
| **Application-consistent** | Quiesce dell'applicazione (VSS Windows, script Linux) | Ripristino garantito senza corruzione |

**VSS (Volume Shadow Copy Service)**: framework Windows per snapshot application-consistent; usato da tutti i backup agent moderni per Exchange, SQL, AD.

### Hot vs Cold backup

- **Hot backup**: sistema in produzione durante il backup; richiede agent application-aware.
- **Cold backup** (offline): sistema spento; massima consistenza ma downtime necessario.
- **CDP (Continuous Data Protection)**: ogni write viene replicata immediatamente; RPO ~0.

---

## Catene di backup

```
Full        → ogni full è indipendente
              [vbk] [vbk] [vbk]

Cumulativa  → Full + diff cumulativa (cresce)
              [vbk] [vib↑] [vib↑↑] [vib↑↑↑]

Incrementale → Full + tutti gli incrementali
              [vbk] [vib] [vib] [vib] [vib]
```

**Forward incremental**: chain con un nuovo full periodico + incrementali; vecchia chain diventa inactive.  
**Reverse incremental**: il backup più recente è sempre un full sintetico; gli incrementali sono reverse (mantengono le versioni precedenti).

---

## Rotazione dei supporti

| Schema | Logica | Vantaggio |
|--------|--------|-----------|
| **Round Robin** | N nastri a rotazione | Semplice |
| **Tower of Hanoi** | Schema binario (1,2,1,3,1,2,1,4,...) | Equilibrio retention/supporti |
| **GFS (Grandfather-Father-Son)** | Daily (Son), Weekly (Father), Monthly (Grandfather) | Standard enterprise; lunga retention |

---

## Regola 3-2-1 e varianti

**Regola 3-2-1** (Peter Krogh, 2009; raccomandata NIST e Carnegie Mellon):
- **3** copie dei dati (1 produzione + 2 backup)
- **2** media diversi (es. disco + nastro)
- **1** copia offsite

**Regola 3-2-1-1-0** (Veeam, era ransomware):
- **3** copie
- **2** media diversi
- **1** offsite
- **1** offline, air-gapped o immutabile
- **0** errori verificati dopo test automatico di recuperabilità

---

## Storage immutabile

**Hard immutability** (fisica):
- CD-R, DVD-R, BD-R — RO by design.
- Tape WORM — write-once; alternativa: tape offline protetti fisicamente.
- Disk WORM.

**Soft immutability** (logica):
- Appliance hardened, SO rinforzato, filesystem con snapshot lock.
- Cloud object storage con Object Lock (S3 Compliance / Governance mode).

**Limiti**: l'immutabilità non protegge da cancellazione o furto; quella soft senza hardening è inutile; se è online è attaccabile.

---

## Backup policy — elementi chiave

- **RPO**: definisce la frequenza di backup (es. RPO 4h → backup ogni 4 ore al massimo).
- **RTO**: definisce il metodo di recovery (tape restore → ore; instant VM recovery → minuti).
- **Finestra di backup**: periodo in cui i backup possono girare senza impattare la produzione.
- **Retention**: quanti restore point mantenere (dipende da compliance, costo storage, probabilità di scoprire un attacco ransomware tardivamente).
- **Catena di dipendenza**: per il ripristino incrementale occorrono full + tutti gli incrementali della chain — chain rotte impediscono il restore.

---

## Backup centralizzato — architettura

```
[Backup Server]  ←→  [Storage Node / Media Server]
       ↕                         ↕
[Backup Clients]           [Backup Storage]
 (agent)                  (Disk / Tape / Cloud)
```

**Modalità**: pull (server preleva dai client) o push (client invia al server).  
**Agenti**: installati sui client per backup application-consistent (DB, Exchange, AD).

---

## Software di backup

| Categoria | Esempi |
|-----------|--------|
| Enterprise proprietario | Veritas NetBackup, IBM TSM/Spectrum Protect, Dell EMC Networker, CA ARCserve |
| SMB proprietario | Veeam (leader cloud-era), Acronis, Commvault |
| Open source | Bacula, Amanda, UrBackup |
| Specifici per VM | Veeam Backup & Replication, Nakivo, Zerto |
| Specifici per container | Veeam Kasten K10 (Kubernetes) |

---

## Dispositivi di backup

### Nastri magnetici (LTO)

| Generazione | Capacità nativa | Velocità |
|-------------|----------------|---------|
| LTO-6 | 2.5 TB | 160 MB/s |
| LTO-7 | 6.4 TB | 300 MB/s |
| LTO-8 | 12 TB | 360 MB/s |
| LTO-9 | 18 TB | 400 MB/s |
| LTO-10 (fut.) | 36 TB | — |
| LTO-11 (fut.) | 72 TB | — |

**Compatibilità**: lettura fino a 2 generazioni precedenti; scrittura fino a 1 generazione precedente.

**Forme**: singolo drive, autoloader (1 drive + magazine), libreria (multipli drive + robot).

### Disco

- **NAS / Appliance**: storage secondario con deduplica, compressione, offload; protocolli SMB/NFS, VTL, DD Boost, HPE Catalyst.
- **B2D2T** (Backup to Disk to Tape): backup su disco come staging rapido, poi migrazione su nastro per archivio.
- **Cloud**: offsite a basso costo; costi di egress alti in restore; S3 Glacier per archivi.

### Tecnologie emergenti

- **DNA Data Storage**: ~2 exabyte in 1 mm³ (paragonabile a 115.000 nastri LTO-9); stabile per millenni.
- **Ceramic Nano Memory**: durabilità estrema.
- **5D Optical Data Storage**: 360 TB per disco ottico; lettura laser ultrarapido.

---

## Piano di Business Continuity

**Ciclo PDCA** (Deming):
1. **Plan**: identificare rischi (BIA — Business Impact Analysis); definire RPO/RTO/MTD; redigere BCP e DRP.
2. **Do**: implementare soluzioni HA, backup, replica, DR site.
3. **Check**: testare regolarmente (DR test, restore test, tabletop exercise).
4. **Act**: migliorare sulla base dei test.

**BCP** (Business Continuity Plan): processo globale con aspetti economici e organizzativi.  
**DRP** (Disaster Recovery Plan): procedure tecniche per emergency response, backup operations e recovery post-disastro.

**Site Reliability Engineering (SRE)**: disciplina Google (Ben Treynor Sloss) che applica ingegneria software a infrastruttura e operazioni; obiettivo: sistemi scalabili e altamente affidabili mediante automazione e SLO.

---

## Articoli correlati

- [[high-availability-e-cluster]]
- [[storage-enterprise]]
- [[sistemi-enterprise-e-integrazione]]
- [[cloud-computing]]
- [[linux-filesystem-storage]]

## Fonti

- `raw/5 - SoluzioniEnterprise.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
