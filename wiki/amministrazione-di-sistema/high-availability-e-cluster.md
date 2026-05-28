---
tags: [high-availability, cluster, raid, failover, load-balancing, split-brain, quorum, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/5 - SoluzioniEnterprise.pdf
---

# High Availability e Cluster

L'**Alta Disponibilità (HA)** è l'insieme di tecniche architetturali che eliminano i Single Point of Failure (SPOF) e aumentano la disponibilità di sistemi e servizi. Le soluzioni spaziano dall'hardware ridondato ai cluster di server, fino a sistemi fault-tolerant completi.

## Punti chiave

- **Obiettivo HA**: eliminare SPOF; aumentare disponibilità (e opzionalmente prestazioni e scalabilità).
- **Formula disponibilità**: `A = MTBF / (MTBF + MTTR)` — agire su MTBF (affidabilità) e MTTR (tempo di ripristino).
- **Livelli di applicabilità**: hardware → OS → servizi → applicazioni.
- **Cluster HA**: tipicamente active-passive (un nodo attivo, uno in standby); richiede quorum/witness per evitare split-brain.
- **Cluster LB**: distribuisce il carico tra nodi attivi (active-active); richiede bilanciatore e gestione sessioni.
- **Split-brain**: condizione in cui i nodi si credono reciprocamente guasti e accedono contemporaneamente allo storage condiviso — causa corruzione dati.

---

## Hardware HA

### Componenti ridondati (server)

| Componente | Soluzione |
|-----------|----------|
| Alimentatori | Ridondati hot-swap (N+1 o 2N) |
| Dischi | RAID + hot-swap + hot-spare |
| Ventole | Hot-swap |
| RAM | RAID memoria (ECC DIMM ridondati) — richiede supporto OS |
| NIC | Teaming/bonding (multipli percorsi attivi o standby) |
| HBA (SAN) | Dual HBA con multipath |

### Hardware HA per SAN

- Global hot spares.
- Multiple data paths (multipath software: Linux DM-Multipath, Windows MPIO).
- Redundant storage processors.
- Ridondanza interna RAID.
- SAN fabric doppie (dual fabric).

### Hardware HA per rete

| Livello | Soluzione |
|---------|----------|
| Switch | Spanning Tree (802.1d), Rapid STP (802.1w) per switch indipendenti; LACP/VLT/Virtual Chassis per switch dipendenti |
| Router | HSRP (Cisco), VRRP (standard), CARP; routing dinamico OSPF/ECMP per percorsi multipli |
| WAN | Linee multiple, BGP multi-AS, SD-WAN |

---

## Fault Tolerant System

Sistema con ridondanza totale dell'hardware che garantisce continuità anche in caso di guasto hardware:
- **High Availability**: in caso di fault il nodo cade e viene riavviato (possibile breve interruzione); dati in RAM potenzialmente persi.
- **Fault Tolerance**: stato della CPU e dati in RAM replicati in tempo reale su nodo gemello; nessuna interruzione percepita — esempi: Stratus ftServer.

---

## Livelli di disponibilità

| % Uptime | Downtime/anno | Tecnologia tipica |
|---------|--------------|------------------|
| 95% | 18 giorni | Server singolo non gestito |
| 99% | 3-4 giorni | Server con componenti ridondati |
| 99.9% | 8.75 ore | HA con cluster 2 nodi |
| 99.99% | 52.5 min | Cluster multi-nodo avanzato |
| 99.999% | 5.25 min | Fault Tolerant System |
| 99.9999% | 31.5 sec | Sistemi mission-critical specializzati |

---

## Cluster — definizione e tipologie

**Computer Cluster**: insieme di macchine che forniscono una piattaforma applicativa scalabile e altamente disponibile, gestite come singola unità logica.

### Tipologie principali

| Tipo | Scopo | Caratteristiche |
|------|-------|----------------|
| **HA / Failover** | Disponibilità | Active-passive; failover automatico; storage condiviso |
| **Load Balancing (LB)** | Prestazioni + HA | Active-active; distribuzione richieste tra nodi; bilanciatore frontend |
| **HPC** | Calcolo parallelo | MPI / PVM; nodi omogenei; interconnect ad alta banda (InfiniBand) |
| **Virtualizzazione** | Consolidamento | Cluster di host hypervisor; live migration delle VM |
| **Grid** | Calcolo distribuito | Nodi eterogenei geograficamente distribuiti |

---

## HA Cluster — architettura e componenti

```
Client ─── [VIP] ──→ Nodo A (ATTIVO)
                      │ heartbeat
                   Nodo B (STANDBY)
                      │
                 [Storage condiviso]
```

**Componenti**:
- **Nodi**: ≥2 server identici o compatibili.
- **Rete heartbeat**: rete dedicata (o seriale) per monitoraggio reciproco dei nodi.
- **Rete erogazione**: rete client-facing con IP Virtuale (VIP) che migra al nodo attivo.
- **Storage condiviso**: SAN/SAS condivisa; un solo nodo alla volta monta il filesystem (per FS non distribuiti).
- **Quorum / Witness**: meccanismo per decidere la maggioranza in caso di partizione — evita split-brain.

### Modalità active-passive vs active-active

| Modalità | Descrizione | Uso |
|----------|-------------|-----|
| **Active-Passive** | Un nodo serve le richieste; l'altro è in standby caldo | DB, ERP, servizi con stato |
| **Active-Active** | Tutti i nodi servono le richieste; failover ridistribuisce il carico | Web server, LB cluster |

### Split-brain e quorum

**Split-brain**: i nodi perdono la comunicazione heartbeat ma sono entrambi funzionanti → entrambi credono di essere il "superstite" → accesso concorrente allo storage → corruzione dati.

**Soluzioni**:
- **Quorum disk** (Disk Witness): lun condivisa usata come arbitro.
- **File Share Witness**: share SMB su server terzo.
- **Nodo di quorum** (numero dispari di nodi: 3, 5, …).
- **STONITH (Shoot The Other Node In The Head)**: spegnimento forzato del nodo che non risponde tramite IPMI/iLO/iDRAC.

---

## Soluzioni HA per Windows

### Microsoft Cluster Server (MSCS) → Failover Clustering

| Versione | Nodi max | Note |
|---------|---------|------|
| MSCS 1.0 "Wolfpack" (1997) | 2 | NT 4.0 |
| Windows 2000 Server | 2 (4 Datacenter) | — |
| Windows 2003 | 8 (più Datacenter) | — |
| Windows 2008+ | 16 | Failover Clustering feature |
| Windows 2012+ | 64 | CSV (Cluster Shared Volumes) |

**CSV (Cluster Shared Volumes)**: namespace distribuito e consistente che permette a tutti i nodi di accedere allo stesso storage condiviso contemporaneamente.

**Terminologia MSCS**:
- **Node**: singolo membro del cluster.
- **Resource**: entità gestita dal cluster (IP, disco, servizio).
- **Group**: insieme di risorse che migrano insieme.
- **Dependency**: relazione tra risorse (una dipende dall'altra).
- **Virtual Server**: punto di accesso al servizio clusterizzato (VIP + nome DNS).

### Soluzioni HA per Linux

- **Pacemaker + Corosync**: stack HA di riferimento; sostituisce il vecchio Heartbeat.
- **Red Hat Cluster Suite (RHCS)**: distribuzione integrata su RHEL.
- **DRBD** (Distributed Replicated Block Device): RAID-1 su rete; replica di blocchi sincrona o asincrona — usato come storage replicato senza SAN condivisa.

---

## Load Balancing Cluster

Distribuisce le richieste client tra più nodi server. Il **bilanciatore** (load balancer) è il punto di ingresso unico.

### Algoritmi di bilanciamento

| Algoritmo | Funzionamento |
|-----------|--------------|
| **Round Robin** | Distribuzione circolare A→B→C→A→... |
| **Least Connections** | Manda la richiesta al nodo con meno connessioni attive |
| **Least Loaded** | Basa la scelta sul carico CPU/memoria |
| **Hashing IP** | Lo stesso client va sempre allo stesso server (persistenza) |

### Soluzioni LB

**Windows**: NLB (Network Load Balancing, max 32 nodi), SLB (da 2016), CLB (middle-tier).

**Linux**:
- **LVS (Linux Virtual Server)**: tre modalità — NAT (modifica src/dst IP), IP-Tunneling, Direct Routing (più scalabile; risposta diretta al client).
- **Nginx / HAProxy**: reverse proxy L7 con supporto HTTP, TCP; configurazione upstream `ip_hash`, `least_conn`, `round-robin`.
- Appliance Kemp, F5, Citrix ADC — spesso basati su Linux.

### Architettura multi-tier

```
Client → [LB] → [WEB1, WEB2, WEB3]
                        ↓
                       [LB]
                        ↓
                [APP1, APP2]
                        ↓
                      [DB]
```

---

## Storage condiviso per cluster

| Soluzione | Descrizione | Accesso concorrente |
|-----------|-------------|-------------------|
| SAN FC/iSCSI | Storage block condiviso tramite fabric | No (richiede cluster FS) |
| SAS condiviso | Active DAS tra 2-4 host | No |
| NAS NFS/SMB | File system su rete | Sì (con lock distribuiti) |
| DRBD | Block device replicato su rete | No (primary/secondary) |
| GFS/OCFS2/CSV | Clustered file system su SAN | Sì |

### Clustered File System

Permettono accesso concorrente multi-nodo allo stesso storage SAN:
- **Microsoft CSV** (Cluster Shared Volumes): integrato in Failover Clustering.
- **Red Hat GFS2** (Global File System 2).
- **OCFS2** (Oracle Cluster FS).
- **GlusterFS, Ceph**: distribuiti (non richiedono SAN condivisa).
- **Lustre**: HPC; scalabile, sicuro, altamente disponibile.

### Cluster senza storage condiviso ("shared nothing")

Alcune applicazioni implementano internamente la replica:
- **Database**: MySQL Cluster, PostgreSQL streaming replication, Oracle RAC, MS SQL AlwaysOn (da 2012), MS Exchange DAG (da 2007), MS DHCP (da 2012).
- **DRBD**: replica blocchi a livello kernel.
- **S2D** (Storage Spaces Direct): Windows; RAIN di disco locale.

---

## Stretched Cluster / Metro Cluster

Cluster HA distribuito su due siti geograficamente separati:
- Storage replicato **sincrono** tra i siti (distanza max ~100 km per latenza ≤10ms).
- Witness su terzo sito per quorum.
- Permette failover automatico verso il sito secondario in caso di disastro al sito primario.

---

## Articoli correlati

- [[storage-enterprise]]
- [[sistemi-enterprise-e-integrazione]]
- [[bcdr-e-backup]]
- [[server]]
- [[linux-filesystem-storage]]
- [[server-windows]]

## Fonti

- `raw/5 - SoluzioniEnterprise.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
