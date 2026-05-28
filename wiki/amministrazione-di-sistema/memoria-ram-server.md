---
tags: [amministrazione-di-sistema, memoria, ram, ecc, nvdimm, persistent-memory, cxl]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - Server_COMPILED.pdf
---

# Memoria RAM nei server

La memoria di un server si colloca nella **gerarchia memoria/storage** (registri → cache → memoria centrale → memoria di massa → memorie removibili): salendo aumentano costo e velocità, scendendo aumenta la capacità. La scelta della RAM bilancia affidabilità, scalabilità, prestazioni, manutenibilità e sicurezza, e oggi si estende verso le **memorie persistenti** che colmano il divario tra DRAM e storage.

## Punti chiave

- Memorie **volatili**: RAM (SDRAM, DDR, GDDR, HBM), SRAM. Memorie **non volatili**: ROM (Mask ROM, PROM, EPROM, EEPROM, Flash), NVRAM, ReRAM, magnetiche, ottiche.
- Affidabilità RAM: memorie con **ECC** (Error Correcting Codes), configurazione **mirror** dei banchi e **hot-swap** (server fascia alta).
- Evoluzione DDR: FPM → EDO → SDRAM → DDR → DDR2/3/4/5; in parallelo HBM/HBM2/HBM2e e OMI. Server attuali con DDR4/DDR5 e fino a 12 slot per socket.
- Scalabilità: più canali per CPU (tipicamente 6), più DIMM per canale (tipicamente 2) → es. Skylake 6 canali × 2 = 12 DIMM/CPU. Quantità installabile fino a 3 TB+.
- Problema **RAM e core**: troppi core in un nodo NUMA possono saturare la banda di memoria; DDR5 fino a 6400 MT/s per pin.
- **In-memory database** (IMDB/MMDB): DBMS che gestiscono i dati nella memoria primaria per velocità maggiori e modelli non relazionali efficienti.

## Caratteristiche e scelta della RAM

- **Affidabilità**: ECC, ridondanza con hot-swap.
- **Scalabilità**: più canali e DIMM per CPU, verso memorie persistenti.
- **Prestazioni**: configurazioni ottimali, architetture NUMA, cache L1/L2/L3 lato CPU.
- **Manutenibilità**: formati standard.
- **Sicurezza**: enclave di sicurezza nei processori Intel e AMD.
- Considerazioni pratiche: **UDIMM vs RDIMM**, come "popolare" i banchi, ottimizzazione del costo per GB (dipende dal tipo di banchi).

## Oltre la RAM: memorie persistenti

- **NVDIMM**: NVDIMM-F (flash, 2014), NVDIMM-N (flash + DRAM sullo stesso modulo), NVDIMM-P (Persistent Memory/PMEM, 2018).
- **Storage Class Memory (SCM)**, **CXL attached memory**, **MMIO**, **High Bandwidth Memory (HBM)**.
- La **Persistent Memory** siede sul memory bus con bassa latenza, alta velocità, persistenza e cache coherence (latenza DRAM ~10¹ ns, NVDIMM-P 10²-10³ ns).

## Modello Big Memory e memory-centric computing

Si passa dal modello classico (compute + memory + performance storage + capacity storage) al **Big Memory model** (DRAM+PM unificati). Si evolve da un modello von Neumann a uno **memory-centric**, con "Near Memory" (vicina al processore) e "Far Memory" in un pool condiviso.

### Memory interconnect
- **Near Memory**: DDR (sistemi piccoli), NVDIMM-N/P, HBM (veloce ma costosa e restrittiva), OMI (alta velocità e grande capacità).
- **Far Memory**: PCIe/NVMe, **CXL**/CCIX (pool di memorie eterogenee, latenze e data rate misti), Gen-Z (per collegare box e rack di storage).
- **CXL is a game changer**: gerarchia HBM → DRAM DDR-connected → memoria CXL-connected, con pooling di DRAM/PMEM/CPU/GPU/xPU.

## Articoli correlati

- [[server]]
- [[hardware-cpu-server]]
- [[storage-server]]
- [[bus-e-reti-server]]

## Fonti

- `raw/2 - Server_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server", sezione Memorie (RAM, gerarchia, ECC, NVDIMM/PMEM, Big Memory, CXL).
