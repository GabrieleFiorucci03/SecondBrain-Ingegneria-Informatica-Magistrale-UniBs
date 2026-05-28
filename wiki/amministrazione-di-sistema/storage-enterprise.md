---
tags: [storage, san, nas, das, iscsi, fibre-channel, raid, hci, nvme, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/5 - SoluzioniEnterprise.pdf
---

# Storage Enterprise

Lo **storage enterprise** comprende tecnologie e architetture per gestire dati a livello aziendale, bilanciando prestazioni, disponibilità, scalabilità e costo. L'evoluzione spazia da soluzioni DAS locali fino a SAN Fibre Channel, NAS scale-out, Object Storage cloud e infrastrutture iperconvergenti.

## Punti chiave

- **Tre paradigmi fondamentali**: Block (DAS/SAN), File (NAS), Object (cloud/archivio) — ciascuno con modello di accesso, latenza e scalabilità diversi.
- **Tassonomia storage**: DAS < NAS < SAN FC < SAN IP; l'object storage scala meglio di tutti (scala tipo 4, exabyte).
- **RAID**: non migliora simultaneamente capacità, prestazioni e ridondanza; al massimo 2 su 3.
- **SAN Fibre Channel**: protocollo dedicato ad alta velocità (8/16/32 Gbps); non è morto — roadmap attiva fino a 2TFC.
- **iSCSI**: comandi SCSI su TCP/IP; standard IETF 2003/2004; abbatte il costo delle SAN FC ma aggiunge overhead TCP.
- **Tipi di dato**: strutturati vs non strutturati; caldi/freddi; 80% dei dati è ROT (Redundant, Obsolete, Trivial).

---

## Classificazione storage

| Tecnologia | Protocollo | Velocità | Tipo dati | Utilizzo |
|-----------|-----------|---------|----------|---------|
| DAS (SAS/SATA) | SAS/SATA | 6/12 Gbps | blocchi | storage locale |
| DAS (NVMe) | NVMe PCIe | >100 GB/s | blocchi | storage locale ultra-rapido |
| NAS | TCP/IP (SMB/NFS) | 1–40 Gbps | file | file sharing |
| SAN FC | Fibre Channel | 8/16/32 Gbps | blocchi | storage condiviso ad alte prestazioni |
| SAN IP | FCIP, iSCSI | 1/10/25/40 Gbps | blocchi | storage condiviso economico |
| CAS | TCP/IP | 1/10 Gbps | archivi | archiviazione immutabile |
| Object Storage | HTTP/HTTPS (S3) | 1/10 Gbps | oggetti | archiviazione scalabile |

---

## Storage tiers

| Tier | Categoria | Tecnologia | Workload |
|------|-----------|-----------|---------|
| **Tier 0** | In-Memory | DRAM/PMEM | latenza microsecondo |
| **Tier 1** | Mission Critical / AFA | NVMe SSD full-flash | OLTP, database, analytics |
| **Tier 2** | Nearline / Hybrid | HDD + SSD cache | file server, ERP, email |
| **Tier 3** | Archivio / Backup | HDD capacity, tape | BCDR, compliance |

**Classificazione dati per temperatura**: Mission Critical → Hot → Warm → Cold. I dati freddi richiedono basso costo e alta durabilità, non bassa latenza.

---

## Paradigmi di accesso ai dati

| Paradigma | Client | Metadati | Scalabilità | Caso d'uso |
|-----------|--------|---------|------------|-----------|
| **Block** | OS via driver | Fixed sys attributes | <1 PB | database, VM disk, OLTP |
| **File** | OS via redirector NFS/SMB | Fixed sys attributes | 10–100 PT | file sharing singolo sito |
| **Object** | Applicazione via API HTTP | Custom metadata | EB (scale-out tipo 4) | archivio, contenuti semi-statici, Big Data |

---

## DAS (Direct Attached Storage)

Storage direttamente collegato a un singolo host.

- **Interno**: SAS, SATA, NVMe (PCIe).
- **Esterno**: SAS Enclosure (HBA SAS + cassetti dischi).
- **Passive Enclosure**: un solo host; RAID implementato da controller o SW.
- **Active Enclosure**: due host possibili (shared DAS); in pratica una SAN SAS senza fabric; max ~4 host.

**Limiti del DAS**: architettura rigida (non scala compute/storage separatamente), inefficiente consolidamento, mancanza di servizi dati avanzati (snap, replica, dedup), API di gestione differenti.

**Velocità tipica** (sistema con PCIe Gen5 NVMe): >60 milioni IOPS, 300 GB/s di throughput.

---

## NAS (Network Attached Storage)

Evoluzione del file server; eroga storage via rete come servizio a file.

**Protocolli**: CIFS/SMB, NFS, NDMP, (FTP).

**Tipologie**:
- **Integrated NAS**: tutto in uno (controller + dischi + SW).
- **Gateway NAS**: front-end di una SAN (separa il protocollo file dalla gestione dei blocchi).

**SMB3 e NFS4 con RDMA**: prestazioni vicine al DAS locale (latenza bassa, throughput molto buono).

**NAS come edge device**: i NAS moderni (es. QNAP) integrano virtualizzazione, routing, gestione rete, sincronizzazione cloud — non solo storage.

---

## SAN (Storage Area Network)

Rete dedicata ad alta velocità che collega host a storage e librerie nastri; la rete si chiama **SAN fabric**.

### Protocolli SAN

**Fibre Channel Protocol (FCP)**:
- Standard ANSI 1988/1994; High Speed Serial Interface.
- Velocità: 8→16→32→64 GFC; roadmap a 128/256/512 GFC e oltre.
- Retrocompatibilità in lettura fino a 2 versioni precedenti.
- Non è "fiber" (fibra ottica) ma "fibre" (termine storicamente incluso rame).

**iSCSI** (Internet SCSI):
- SCSI command set su TCP/IP; RFC 3720 (2004).
- Layer protocol stack: SCSI → iSCSI → TCP → IP → Ethernet.
- Indirizzi: IP + IQN (iSCSI Qualified Name, formato `iqn.yyyy-mm.reversed.domain:identificatore`).

**Protocolli aggiuntivi**: FCoE (FC su Ethernet), FCIP (FC su IP tunneling WAN), NVMeOF.

### Elementi di una SAN

| Componente | Ruolo |
|-----------|-------|
| **HBA (Host Bus Adapter)** | Scheda nel server (initiator); ha WWN a 64 bit (analogo MAC) |
| **SAN Switch/Hub** | Fabric; nei sistemi FC gestisce il Zoning |
| **Storage Array** | Target; espone LUN tramite Storage Processor (SP) |
| **LUN (Logical Unit Number)** | Unità di blocchi esposta dall'array all'host |
| **Zoning** | Sicurezza FC: definisce quali porte possono comunicare |
| **LUN Masking** | Controllo accesso: quali host vedono quale LUN |

### Topologie Fibre Channel

- **Point-to-Point**: collegamento diretto host-storage.
- **Arbitrated Loop (FC-AL)**: loop con max 126 dispositivi.
- **Switched Fabric**: standard enterprise; switch FC interconnessi tramite E-Port.
- **Public Loop with Hub**: hub FC connesso a switch.

### Funzionalità avanzate SAN

- **RAID Group**: raggruppamento di dischi con livello RAID.
- **Storage Group**: raggruppamento di LUN assegnate a un host.
- **Remote Mirror**: replica sincrona o asincrona su SAN remota.
- **Snapshot**: copia point-in-time (immutabile o R/W clonata).
- **MultiPath / PowerPath**: ridondanza dei percorsi; gestione failover automatico.

---

## NVMe Over Fabric (NVMeOF)

Estensione di NVMe su rete per ridurre latenza rispetto a iSCSI e FCP:

**Transport disponibili**:
- **NVMe/FC**: FC come fabric (più diffuso in enterprise).
- **NVMe/RDMA**: su InfiniBand (IB), RoCE o iWARP.
- **NVMe/TCP**: più semplice da implementare su infrastruttura IP esistente.

RDMA è supportato anche da NAS (SMB3 e NFSoRDMA) e da SAN (SRP, iSER).

---

## Block storage — concetti fondamentali

### SCSI Address (b, t, l)
- **Bus**: controller (HBA SAS, FC card, NIC iSCSI).
- **Target**: storage resource.
- **LUN**: SCSI device interno al target.

### LUN & LBA
- **LUN**: identificatore logico del dispositivo.
- **LBA (Logical Block Address)**: offset nel LUN; indirizzamento remapping tramite volume pool.

### Initiator / Target
Modello client-server: l'**initiator** (host) invia comandi SCSI; il **target** (storage) li riceve ed esegue.

---

## RAID

**Redundant Array of Independent Disks** (brevetto IBM 1978); implementabile in hardware (controller ASIC) o software (Linux MD/LVM, Windows Storage Spaces).

### Livelli standard

| Livello | Descrizione | Min dischi | Fault tolerance | Efficienza |
|---------|-------------|-----------|----------------|-----------|
| RAID 0 | Striping | 2 | Nessuna | 100% |
| RAID 1 | Mirroring | 2 | 1 disco | 50% |
| RAID 5 | Striping + parità distribuita | 3 | 1 disco | (N-1)/N |
| RAID 6 / DP | Striping + doppia parità | 4 | 2 dischi | (N-2)/N |
| RAID 10 | Mirror poi striping | 4 (pari) | 1 per coppia | 50% |
| RAID 50 | RAID5 poi striping | 6 | 1 per gruppo RAID5 | var. |

**Penalità di scrittura RAID** (write penalty = I/O aggiuntivi per aggiornare la parità):

| RAID | Penalty |
|------|---------|
| 1 | 2 |
| 5 | 4 |
| 6 | 6 |
| 10 | 2 |

Formula: `Functional IOPS = (Raw IOPS × Write%) / RAID_Penalty + (Raw IOPS × Read%)`

### Implementazioni speciali

- **Intel VROC** (Virtual RAID on CPU): RAID 0/1/5/10 su NVMe, implementato nel processore Skylake-X; >2.5M Read IOPS con 4 Optane SSD.
- **ZFS RAID-Z**: filesystem che include parità (non richiede controller RAID separato).
- **dRAID / Distributed Spare**: spare distribuito su tutti i dischi (IBM, ZFS 2.1+) — rebuild più veloce.
- **RAIN** (Redundant Array of Independent Nodes): ridondanza a livello di nodo (non disco); approcci mirroring o Erasure Coding (Reed-Solomon N,K).

### Non-RAID

- **JBOD**: dischi indipendenti, nessuna ridondanza.
- **SPAN**: volumi concatenati senza ridondanza.
- **MAID**: Massive Array of Idle Disks per archivio a basso costo.

---

## Tiering / Caching

| Tecnica | Descrizione |
|---------|-------------|
| **RD Cache** | Copia Read-Only dei blocchi caldi in SSD/RAM |
| **RD/WR Cache** | Copia Read-Write (write-back); rischio dati in caso di guasto senza batteria |
| **Tiering (HSM)** | Spostamento automatico dei blocchi tra tier in base all'utilizzo; tutto su storage persistente |

**Tier HDD interni vs esterni**: i settori esterni di un HDD hanno velocità lineare maggiore — usati come tier più caldo nei sistemi ibridi.

---

## Funzionalità avanzate di storage

| Funzionalità | Descrizione |
|-------------|------------|
| **Snapshot** | Copia point-in-time; immutabile (R/O) o clonata (R/W); non è un backup |
| **Replica sincrona** | Write completata solo dopo ACK dal sito secondario; distanza max ~100 km (latenza ≤10ms) |
| **Replica asincrona** | Write completata sul primario; secondario aggiornato dopo; nessun limite di distanza |
| **Deduplicazione** | Inline o post-process; a livello blocco o file |
| **Compressione** | Hardware o software; blocco o file |
| **Cifratura (DARE)** | Hardware (SED, enclosure, BC) o software (controller, OS, applicazione); richiede KMS |
| **Thin provisioning** | Allocazione fisica solo al momento della scrittura effettiva |

---

## Architetture scale-out storage

| Tipo | Caratteristiche | Esempi |
|------|----------------|--------|
| **Type 1 — Clustered** | Dual controller, Shared Meta, scale up/down | NAS tradizionale, midrange array |
| **Type 2 — Tightly Coupled** | Multi-controller grid, Shared Meta, lineare | Isilon (Dell EMC) |
| **Type 3 — Loosely Coupled** | Controller indipendenti, Distributed Meta | GlusterFS, Ceph |
| **Type 4 — Distributed Share Nothing** | Non-transactional commits | HDFS, Object Storage |

### HCI (Hyperconverged Infrastructure)

Evoluzione degli scale-out Type 3: ogni nodo integra compute (CPU/RAM), storage locale (NVMe/SSD/HDD) e rete. Il software (hypervisor + SDS) virtualizza tutte le risorse.

- Ogni nodo ha: Controller VM, Hypervisor, Storage Controller (iSCSI/SMB3/NFS/vSAN).
- Scalabilità: aggiungendo nodi aumentano simultaneamente compute e storage.
- Svantaggi: performance inferiori (15-25K IOPS vs AFA), compute scala insieme allo storage, Hypervisor Tax.

**Esempi**: VMware vSAN, Nutanix, HPE SimpliVity, Microsoft S2D.

### SDS (Software Defined Storage)

Separazione del piano di controllo dall'hardware sottostante:
- **Automazione** e API standard.
- **Percorso dati virtualizzato**: interfacce blocco, file e oggetti.
- **Scalabilità** orizzontale e verticale.
- Esempi: Ceph, GlusterFS, MinIO, OpenIO.

### Disaggregated / Composable Storage

- **Disaggregated**: compute e storage separati logicamente senza modificare connessioni fisiche.
- **Composable**: fabric PCIe/Ethernet collega pool separati di compute, storage, GPU, rete — componibile on-demand via software.

---

## Object Storage (Cloud / On-premises)

**Oggetto = dati + metadati nativi + metadati custom + ID**.

Provider cloud: Amazon S3, Azure Blob, GCP Storage, IBM COS, Wasabi.
On-premises: Ceph, Scality, MinIO, OpenIO, SwiftStack.

**Classi S3**:
- Standard (active data, 99.99% availability).
- Standard-IA (infrequent access).
- One Zone-IA.
- Glacier / Glacier Deep Archive (archivio, latenza minuti/ore).

**Durability**: 99.999999999% (11 nines) tramite erasure coding multi-AZ.

---

## Confronto finale storage

| Tipo | Protocolli | Latenza | Scalabilità |
|------|-----------|---------|-------------|
| DAS SAS/SATA | SAS/SATA | Bassa/Media | Limitata (100 TB) |
| DAS NVMe | NVMe | Molto bassa | Limitata (1–100 TB) |
| SAN FC / NVMeOF | FC, NVMeOF | Bassa | Discreta (<1 PB) |
| SAN iSCSI | iSCSI | Bassa/Media | Discreta (<1 PB) |
| NAS SMB3/NFS4 RDMA | RDMA | Bassa | Buona (10–100 PT) |
| Object Storage | HTTPS S3 | Media-alta | Eccellente (EB) |

---

## Articoli correlati

- [[storage-server]]
- [[linux-filesystem-storage]]
- [[bus-e-reti-server]]
- [[high-availability-e-cluster]]
- [[sistemi-enterprise-e-integrazione]]
- [[cloud-computing]]

## Fonti

- `raw/5 - SoluzioniEnterprise.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
