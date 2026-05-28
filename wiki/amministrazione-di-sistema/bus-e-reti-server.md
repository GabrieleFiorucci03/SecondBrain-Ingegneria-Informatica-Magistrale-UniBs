---
tags: [amministrazione-di-sistema, bus, pcie, cxl, rete, nic, sr-iov]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - Server_COMPILED.pdf
---

# Bus di sistema e rete nei server

I **bus di sistema** collegano CPU, memorie e periferiche; le **schede di rete** (NIC) connettono il server alla rete. Entrambi sono punti critici per le prestazioni e la scalabilità di un server, e seguono una rapida evoluzione verso interconnessioni seriali ad alta banda e schede di rete "intelligenti" che spostano elaborazione vicino ai dati.

## Punti chiave

- **PCI Express (PCIe)**: bus seriale che sostituisce PCI, PCI-X e AGP. Versioni: 2.0 (2007, 500 MB/s per linea), 3.1 (2010, 1 GB/s), 4.0 (2017, ~2 GB/s, fino a 16 canali = 31,5 GB/s), 5.0 (2019, 63 GB/s a 16x), 6.0 (2022, 121 GB/s a 16x).
- Evoluzione dei bus: PCI → PCIe → NVMe → NVMeoF → **CXL**, con rami OpenCAPI, CCIX, Gen-Z. **CXL è un game changer** per unificare memorie e acceleratori.
- **Bus esterni**: Thunderbolt 3 / USB4 (40 Gb/s), USB 3.x (4,8/10/20 Gb/s), USB 2.0 (480 Mb/s), eSATA (3 Gb/s), Giga/10/40G Ethernet (con PoE).
- Schede di rete: famiglia **IEEE 802.3** — 1 Gbps (802.3z fibra, 802.3ab rame), 10 Gbps (802.3ae/an), 40/100 Gbps (802.3ba), 25 Gbps (802.3bq/by).
- Forme della NIC: **LOM** (LAN-on-Motherboard), **NDC** (Network Daughter Card), Add-on NIC.
- **Partizionamento** delle schede: physical/logical/virtual functions, offload; tecniche **NPAR** (Network Partition) e **SR-IOV** (Single Root – IO Virtualization).

## Modelli di connessione e accelerazione

- **Server connection models**: Top-of-Rack (switch in cima al rack) ed End-of-Row (switch a fine fila).
- **NPAR**: crea più interfacce Ethernet native che condividono una singola porta fisica.
- **SR-IOV**: standard che presenta un singolo device PCIe (Physical Function) come più device indipendenti (Virtual Function) a SO e hypervisor — fondamentale per la [[virtualizzazione]].
- **Accelerazione hardware**: Jumbo Frame; offload con **TOE** (TCP Offload Engine) e Intel I/OAT.
- **SmartNIC**: FPGA/SoC sulla NIC, infrastruttura data center software-defined su chip; es. NVIDIA BlueField **DPU**. Varianti ASIC-based, FPGA-based, SoC-based (NIC + CPU).

## Articoli correlati

- [[server]]
- [[hardware-cpu-server]]
- [[memoria-ram-server]]
- [[storage-server]]
- [[virtualizzazione]]

## Fonti

- `raw/2 - Server_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server", sezioni Bus di sistema e Rete (PCIe/CXL, bus esterni, NIC, NPAR/SR-IOV, SmartNIC).
