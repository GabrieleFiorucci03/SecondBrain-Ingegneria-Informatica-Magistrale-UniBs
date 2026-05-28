---
tags: [amministrazione-di-sistema, linux, processi, servizi, pacchetti, ntp, udev]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - ServerLinux_COMPILED.pdf
---

# Gestione di un server Linux

La gestione operativa quotidiana di un server Linux comprende clock, hardware/driver, software (pacchetti), processi e servizi. Tutto è amministrabile da console/CLI in locale o da remoto (SSH), seguendo il principio del minimo privilegio.

## Punti chiave

- **Clock**: due orologi indipendenti (hardware/CMOS e software/kernel). Il kernel tiene il tempo universale come secondi dall'epoch (1/1/1970). Fusi: UT/UTC/GMT, CET/CEST. Comandi `date` (clock software), `clock`/`hwclock` (hardware); file `/etc/localtime`, `/etc/adjtime`; sincronizzazione via **NTP**.
- **Hardware/driver**: driver a livello kernel come moduli (`modprobe`, `lsmod`, `rmmod`); driver applicativi (stampa, video).
- **Device naming**: device a blocchi IDE (`/dev/hda`), SCSI/SATA (`/dev/sda`), NVMe (`/dev/nvme0n1`); partizioni MBR (`/dev/hda1`...). **udev** genera eventi dal kernel, il servizio `udevd` li riceve e le regole creano nomi persistenti `/dev/disk/by-*`; `blkid` legge label/UUID.
- **Pacchetti**: contenitori di binari, configurazioni e script con **dipendenze**. Due famiglie: **DEB** (Debian/derivate: `dpkg`, front-end `apt`) e **RPM** (RHEL/SUSE: `rpm`, front-end `dnf`/`yum`/`urpmi`). DNF risolve le dipendenze meglio di YUM (usa `libsolv`).
- **Processi**: esecuzione foreground/background, `nohup`, segnali con `kill`, visualizzazione con `ps aux`/`ps -ef`/`pstree`/`top`, priorità con `nice`. Scheduling con **cron** (`crontab -e`, `/etc/crontab`) e **at**.
- **Servizi (demoni)**: processi in background senza I/O interattivo. Avvio a mano (`/etc/rc.d/init.d/<demone> start`) o via script. Configurazione: link legacy, `update-rc.d` (Debian), `chkconfig` (RedHat), `systemctl list-unit-files` (systemd). Restart con `service <d> restart` / `systemctl <d> restart`. Attivare solo i servizi necessari (crond, syslogd, network, sshd).

## Comandi essenziali

Tra i comandi fondamentali (Top 50): navigazione (`ls`, `pwd`, `cd`), file (`mkdir`, `mv`, `cp`, `rm`, `touch`, `ln`), archivi (`tar`, `zip`/`unzip`), testo (`cat`, `less`, `grep`, `head`, `tail`, `sort`, `diff`), sistema (`uname`, `whoami`, `ps`, `top`, `kill`, `df`, `mount`, `chmod`, `chown`), rete (`ssh`, `wget`, `traceroute`, `ifconfig`, `iptables`, `ufw`), pacchetti (`apt`/`yum`/`pacman`/`rpm`), privilegi (`sudo`), utenti (`useradd`, `passwd`). Filosofia di aiuto: **RTFM** (Read The Friendly Manual: `man`, guide distro, TLDP) e **UTSL** (Use The Source, Luke).

## Articoli correlati

- [[server-linux]]
- [[linux-installazione-boot]]
- [[linux-utenti-autenticazione]]
- [[linux-log-monitoraggio-rete]]

## Fonti

- `raw/2 - ServerLinux_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server Linux", sezioni clock/NTP, hardware/udev, gestione software (DEB/RPM), processi e servizi.
