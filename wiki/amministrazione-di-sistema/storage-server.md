---
tags: [amministrazione-di-sistema, storage, hdd, ssd, raid, nvme, iops]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - Server_COMPILED.pdf
---

# Storage nei server

Lo **storage** di un server gestisce la memoria di massa (secondaria) e, sempre più, livelli intermedi vicini alla RAM. La scelta bilancia affidabilità (con soluzioni RAID), scalabilità (capacità e densità in crescita), prestazioni (dischi e bus più veloci), manutenibilità (formati standard, hot-plug) e sicurezza (dischi **SED**, Self-Encrypting Drive). Lo storage si organizza in **tier** secondo prestazioni e criticità.

## Punti chiave

- Tipi di disco: **HDD** (meccanici) e **SSD** (Flash). Tipi di storage: **DAS** (Direct Attached), **NAS** (Network Attached, su Ethernet/Wi-Fi), **SAN** (Storage Area Network, rete dedicata ad alta velocità), nastri magnetici.
- Prestazioni HDD determinate da densità, velocità testine, RPM, canali, cache/controller, transfer rate, file system. Capacità SATA ~20 TB (2022), poche aziende produttrici.
- Tecnologie HDD: Perpendicular Recording (2006), Advanced Format, **HAMR** (Heat-Assisted Magnetic Recording), MACH.2 (doppie testine), blocchi da 4K. Roadmap Seagate: PMR fino a 20 TB, HAMR verso 30/40/50+ TB.
- SSD/NAND per densità di celle: **SLC** (1 bit), **MLC** (2), **TLC** (3), **QLC** (4) — più bit/cella = più capacità ma minore retention; **3D XPoint** (Optane). Emerging memories: MRAM, PCM, ReRAM, FRAM.
- **HDD vs SSD**: shift economico verso il flash intorno al 2016; SSD offrono più capacità/prestazioni con costo/TB conveniente in molti casi.
- **IOPS** (Input/Output Operations Per Second): metrica di prestazione e dimensionamento dello storage (random/sequential/total, read/write).

## Caratteristiche dello storage

- **Affidabilità**: variabile per tecnologia, soluzioni **RAID**.
- **Scalabilità**: crescita di capacità e densità.
- **Prestazioni**: dischi e bus più veloci.
- **Manutenibilità**: formati standard, hot-plug nei server, nuovi formati.
- **Sicurezza**: dischi **SED**.

## Disk bus e form factor

- **Bus**: P-ATA (133 MB/s), S-ATA/SATA II (150/300 MB/s), SCSI (Ultra-320), **Fibre Channel** (1-32 Gb/s), **SAS** (Serial Attached SCSI: SAS-1 3 Gb/s con compatibilità SATA II, SAS-2/3 6-12 Gb/s), PCIe/NVMe/SCM/DIMM, U.2, M.2.
- **Form factor**: HDD LFF 3.5" e SFF 2.5"; **EDSFF** (Enterprise & Data Center SSD Form Factor): E1.L (Long), E1.S (Short), Samsung NF1. Migrazione M.2→E1.S, U.2→E3/E1.L.

## NVM Express (NVMe)

**NVMe** definisce un'interfaccia di queuing, command set e feature set ottimizzati per SSD PCIe, scalabile da client a enterprise. Standardizzazione → driver standard, feature set coerente, ecosistema. Supporta 64.000 comandi con 64.000 queue depth. Vantaggi: parallelismo e throughput migliori (3 SSD NVMe saturano un link 100 Gbps), latenza migliorata ~1000× vs HDD. Problemi aperti (latenza): workload isolation, **WAF** (Write Amplification Factor), background processes; soluzioni: **ZNS** (Zoned Name Space), **FDP** (Flexible Data Placement), software-defined flash.

## RAID, boot e tiering

- **RAID**: disco "logico" basato su più dischi fisici per ridondanza e/o prestazioni; implementazione hardware o software.
- **Boot device**: dischi locali (su controller RAID), schede ad hoc (BOSS card, ISDM), chiavi USB/SATADOM, **Boot from SAN**, **Boot from LAN** (PXE Boot).
- **Storage tiers**: Tier 0 (prestazioni paragonabili alla RAM, PMEM), Tier 1 (storage primario veloce, SSD), Tier 2/3 (primario o secondario, buono in lettura, non per business-critical), Archive tier; gestione con **HSM** (Hierarchical Storage Management).
- **Cache**: minimizza i colli di bottiglia tra CPU/memorie e tra disco/memorie; tipi hardware/software, policy Read Ahead e Write Through/Write Back. Disk cache tipicamente implementata dal SO (flash locali su SATA/SAS, PCIe, bus memoria, NVDIMM/PMEM).

## Piramide delle memorie

CPU → RAM/DIMM → NVDIMM/PMEM → SSD (Flash) → HDD (Magnetic): salendo crescono costo e velocità, scendendo la capacità. La **Persistent Memory** colma il gap tra DRAM e storage.

## Articoli correlati

- [[server]]
- [[hardware-cpu-server]]
- [[memoria-ram-server]]
- [[bus-e-reti-server]]

## Fonti

- `raw/2 - Server_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server", sezione Storage (HDD/SSD, RAID, NVMe, IOPS, tiering, bus dischi).
