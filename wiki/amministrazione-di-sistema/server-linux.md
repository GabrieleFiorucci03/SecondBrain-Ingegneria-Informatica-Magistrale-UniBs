---
tags: [amministrazione-di-sistema, linux, gnu, distribuzioni, kernel, software-libero]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - ServerLinux_COMPILED.pdf
---

# Server Linux

**GNU/Linux** è un sistema operativo libero, nato dall'incontro tra il progetto **GNU** (Stallman, FSF 1984; GNU 1989) e il **kernel Linux** (Torvalds, 1991), sulle fondamenta della tradizione **Unix** (Thompson, Ritchie, Kernighan; Unix 1971 → dialetti System V/BSD/Minix). È oggi la piattaforma server dominante insieme a Windows, apprezzata per affidabilità, scalabilità, manutenibilità e licenza libera. Questo articolo è l'hub del modulo Linux: rimanda agli articoli operativi su installazione, gestione del sistema, utenti, filesystem e rete.

## Punti chiave

- **Tipi di software**: OpenSource (Public Domain, XFree86 style, licenze artistiche, copyleft GPL) e Free (FSF) da un lato; Proprietario (chiuso, shareware) dall'altro.
- **Caratteristiche dei server Linux**: alta affidabilità/stabilità (uptime di anni, rari kernel panic), scalabilità orizzontale e verticale, buone prestazioni, manutenibilità (tutto configurabile via file di testo e da remoto), licenza free.
- Il **kernel** è il "cuore" di Linux. Numerazione moderna con filoni longterm, stable, snapshot, mainline, next (non più pari/dispari).
- Una **distribuzione** è un Linux "funzionante" (kernel + utility + applicativi). Differiscono per licenza, supporto/certificazioni, organizzazione file/servizi, target workstation/server.
- Distribuzioni server diffuse: **Ubuntu**, **Debian**, **RHEL**, **SLES**, **Rocky/AlmaLinux**, Amazon Linux, Oracle Linux. Requisito chiave: supporto a lungo termine.
- **Standard aperti**: **FHS** (Filesystem Hierarchy Standard) e **LSB** (Linux Standard Base).

## Storia e filosofia

La linea temporale: Unix (1971) → dialetti SV/BSD/Minix; FSF (1984) → GNU (1989); Linux di Torvalds (1991) → GNU/Linux. In parallelo l'hardware: PC Intel (1981) → i386. Il software libero (FSF) garantisce le libertà d'uso/studio/modifica/ridistribuzione; l'OpenSource include il copyleft (GPL) che propaga la libertà alle opere derivate.

## Kernel e architettura di Linux

Il kernel è seguito storicamente da Torvalds (kernel.org). Architettura a livelli: **User Application** → **System Call Interface** → sottosistemi del kernel (Virtual File System, Memory Management, Process Management, Networking System, Module Management) → **Device Drivers** → hardware. I sorgenti del kernel sono suddivisi in parti (SCI, PM, VFS, MM, Network Stack, Arch, Device Drivers); filesystem virtuali come `/proc`, `/sys`, `/dev` (udev) espongono lo stato del kernel.

## Distribuzioni e micro-Linux

- **Enterprise**: RHEL (con Oracle Linux; CentOS sostituito da Rocky/AlmaLinux), SuSE/openSUSE, Debian/Ubuntu Server, VMware PhotonOS, Amazon Linux.
- **Micro-Linux**: sistemi minimali embedded (**Busybox**); per microserver orientati ai container (CoreOS, PhotonOS, Atomic); per virtual appliance (SuSE Studio); "dietro le quinte" del cloud pubblico (Microsoft CBL-Mariner, Google/AWS).
- Lo strumento **Linux From Scratch** permette di costruire una distribuzione da zero.

## Tool per l'amministratore

Tutto è configurabile via script/file di testo e da remoto. Tool specifici: testo/grafica/web (**linuxconf**, **webmin**), tool delle distribuzioni, di KDE/Gnome, proprietari (YaST). Gestione web-oriented moderna: **Webmin** e **Cockpit** (interfaccia grafica web per server, adatta sia a neofiti sia a esperti).

## Novità delle distribuzioni recenti

Integrazione di **SELinux** (default in modalità restrittiva, security label del filesystem), gestione configurazione con **systemd**/etcd, log con **journald**, gestione risorse con **cgroups** (control groups) e virtual switch, firewall con **firewallD**/**nftables**, virtualizzazione con **container** e **KVM** (vedi [[virtualizzazione]]).

## Articoli correlati

- [[server]]
- [[linux-installazione-boot]]
- [[linux-gestione-sistema]]
- [[linux-utenti-autenticazione]]
- [[linux-filesystem-storage]]
- [[linux-log-monitoraggio-rete]]
- [[server-windows]]
- [[sicurezza-di-sistema]]

## Fonti

- `raw/2 - ServerLinux_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server Linux" (storia GNU/Linux, software libero, kernel, distribuzioni, standard, tool).
