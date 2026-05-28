---
tags: [amministrazione-di-sistema, hardware, cpu, numa, gpu, fpga, soc, sicurezza]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - Server_COMPILED.pdf
---

# Hardware e CPU dei server

L'hardware di un server segue come modello di riferimento l'**architettura di von Neumann**, evoluta in un'**architettura a bus multi-livello** (CPU, memoria centrale e interfacce I/O collegate da bus). Su questa base si innesta l'evoluzione moderna: più bus e più veloci, passaggio da UMA a NUMA, processori dedicati (xPU), System on Chip (SoC), memorie persistenti, SSD e la distinzione tra risorse locali e remote. La scelta della CPU bilancia affidabilità, scalabilità, prestazioni, manutenibilità e sicurezza.

## Punti chiave

- **UMA** (Uniform Memory Access): tutte le CPU accedono alla memoria con la stessa latenza. **NUMA** (Non-Uniform Memory Access): ogni CPU ha memoria "vicina" dedicata, più veloce di quella remota. I processori moderni sono internamente UMA di multicore ed esternamente NUMA.
- Terminologia CPU: **socket** (CPU fisica), **nodo NUMA** (parte di CPU con accesso condiviso alla RAM), **core** (unità di elaborazione), **processore logico** (unità logica via Hyper-Threading).
- Tecnologie per le prestazioni: **SMP** (Symmetric Multiprocessing), **multi-core** (fino a 64+ core), **HTT** (Hyper-Threading Technology), **Turbo Boost**, cache L1/L2/L3 (fino a ~100 MB).
- Piattaforme a **64 bit**: x64 (AMD64/x86-64, Intel EM64T) con modalità legacy 32-bit/compatibility/full 64-bit; Itanium (IA-64) ormai abbandonato.
- Le **CPU sono nuove superfici d'attacco**: bug come **Spectre** e **Meltdown** (2018), L1TF, Foreshadow, ZombieLoad; mitigazioni via microcode, patch SO/hypervisor, fix hardware (Intel 9ª gen).
- Oltre la CPU: **GPU/GPGPU**, **FPGA**, e famiglia **xPU** (ASIC, TPU, NPU, IPU, DPU) come acceleratori specializzati.

## Architettura e prodotti

Il modello chipset classico separa il **North Bridge** (Memory Controller Hub, collegato a CPU, memoria, PCI Express graphics via System/Front Side Bus) dal **South Bridge** (I/O Controller Hub: SATA, PCI, USB, audio). La crescita segue la **legge di Moore** con gap tecnologico tra le varie tecnologie. Prodotti server tipici: **Intel Xeon** (Xeon Scalable: Cooper Lake, Ice Lake, Sapphire Rapids; Xeon Max con HBM) e **AMD EPYC** (architettura a chiplet/die con CCX). Evoluzione: DDR4/DDR5, PCIe x4/x8/x16, Ethernet 10-100 Gbps, TCPA (Trusted Computing Platform Alliance).

## Evoluzione del calcolo

- **CPU** — coprocessore matematico, istruzioni SSE/SSE2; modello SISD; resta il centro del calcolo.
- **GPU/GPGPU** — *General-Purpose computing on GPU*: CUDA, OpenCL; modello SIMD; eccelle nel floating point vettoriale, penalizzata da branch/eccezioni. Le prestazioni GPU-computing crescono ~2× l'anno (vs ~1.1× single-thread CPU).
- **FPGA** — *Field-Programmable Gate Array*: circuito riprogrammabile sul campo che "ricabla" il chip invece di eseguire software; modello MIMD; ottimo per parallelismo e streaming I/O.
- **xPU** — **ASIC** (Application Specific IC), **TPU** (Tensor), **NPU** (Neural), **IPU** (Intelligent, Graphcore), **DPU** (Data Processing Unit, processing nella NIC; NVIDIA, Intel).
- Server con GPU: Dell PowerEdge XE9680, HPE Cray XD670.

## Architetture degli instruction set

- **CISC** (Complex Instruction Set Computer): es. x86; unità di memoria per le istruzioni, compilatore semplice, decoding difficile, più spazio disco.
- **RISC** (Reduced Instruction Set Computer): registri per i dati, compilatore complesso, pipelining facile, calcoli più veloci; es. ARM, MIPS, Power, SPARC, RISC-V.
- **ARM** (Advanced RISC Machines): alternativa a x64 dal datacenter (Ampere Computing, ARM Neoverse N1) all'edge (Raspberry Pi, NVIDIA Jetson). **RISC-V** è una ISA open source.

## System on Chip (SoC)

Integrazione di CPU, GPU, NPU e memoria in un singolo chip. Esempi: **Apple M1** (SoC a 64-bit RISC, 2020, con UMA condivisa tra CPU/GPU/NPU) e **Intel Xeon Max** (HBM on-package per workload AI/VM).

## Articoli correlati

- [[server]]
- [[memoria-ram-server]]
- [[storage-server]]
- [[bus-e-reti-server]]
- [[sicurezza-di-sistema]]

## Fonti

- `raw/2 - Server_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server", sezione Hardware/CPU (architettura, NUMA/UMA, processori, GPU/FPGA/xPU, ISA, SoC).
