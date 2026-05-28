---
tags: [amministrazione-di-sistema, linux, installazione, boot, systemd, bootloader, lvm]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - ServerLinux_COMPILED.pdf
---

# Installazione e avvio di un server Linux

L'installazione di Linux dipende dalla distribuzione ma segue passi comuni; l'avvio (boot) è una sequenza che va dal firmware al primo processo utente. Per un server contano scelte ponderate su partizionamento, filesystem, pacchetti e servizi, guidate dal **principio del minimo privilegio**.

## Punti chiave

- Passi comuni di installazione: scelta di sorgente, destinazione, pacchetti, servizi (demoni), rete, password, **boot loader**.
- Partizionamento server: più partizioni consigliate (Swap, `/`, `/usr`, `/var`, `/srv`, ...); alternative/complementi: **RAID** e **LVM** (vedi [[linux-filesystem-storage]]).
- **LVM** (Logical Volume Manager): astrazione su partizioni/dischi raw con componenti PV (Physical Volume), VG (Volume Group), LV (Logical Volume), PE (Physical Extent ~4MB). Standard in tutte le distribuzioni.
- Boot IA-32 tradizionale: firmware **BIOS** → loader nel Boot Record (MBR su HDD) → caricamento SO. Bootloader Linux: **LILO** (`/etc/lilo.conf`), **Grub** (standard de facto).
- Avvio Unix storico: il processo **init** (PID=1) legge `/etc/inittab`, attiva terminali ed esegue script di avvio (`/etc/rc.d/`) secondo l'**initlevel** (0 halt, 1 single-user, 3 multi-utente, 5 X11, 6 reboot). Tutti i processi sono figli di init.
- **systemd**: evoluzione di init (supporta script SysV/LSB), strutturato in **unit** (`.service`, `.mount`, `.device`, `.socket`); comandi `systemctl start|stop|status|enable|is-enabled`.

## Requisiti e scelta della distribuzione

Requisiti minimi storici risibili (i386, 2 MB RAM); la configurazione ideale "dipende". Scelta distribuzione: stabile o sperimentale, antiquata o recente, architettura e lingua. Si ottiene via Internet (linuxiso.org), edicola o LUG.

## Scelta del filesystem e altre scelte

Tipi: Swap, tradizionale (ext2), transazionale/journaling (ext3, ext4, ReiserFS, XFS, JFS), Btrfs, ZFS (dettagli in [[linux-filesystem-storage]]). Altre scelte guidate dal **minimo privilegio**: installare solo i pacchetti necessari, attivare solo i servizi necessari, scegliere password robuste, nome macchina, driver aggiuntivi.

## Modalità rescue

In caso di emergenza (password root persa, bootloader non funzionante, SO incompleto, tabella partizioni mancante, disco difettoso): partizione di emergenza, **CD Rescue** (distribuzione live, opzione di boot, distribuzioni specifiche per il rescue).

## Avvio, bootstrap e systemd

Nel boot Linux/Unix il kernel viene caricato in memoria e vi resta sempre; con il kernel si "vedono" processi e filesystem, e il primo processo è **init**. Il bootstrap RedHat (legacy) è variante System V: 6 runlevel con directory corrispondenti, link in `/etc/rc.d/init.d` ("S" start, "K" kill), script fuori standard `rc.sysinit` e `rc.local`. I tempi di avvio dipendono da inizializzazione hardware (POST), caricamento kernel e processo init; si ottimizzano con pochi servizi e init "snello" (parallel init, upstart, initng), oggi superati da **systemd**.

## Arresto del sistema

Un SO **non** si spegne togliendo l'alimentazione (la cache rende il filesystem incoerente). Comandi: `shutdown -now`/`halt` (spegnimento), `shutdown -r -now`/`reboot` (riavvio). Durante lo shutdown: processi uccisi, filesystem sincronizzati e smontati, spegnimento via APM. Se il sistema non risponde: soluzioni drastiche (alimentazione, reset) o "soft" via **SysReq** (Alt-SysReq-U/S/B per remount-ro/sync/reboot).

## Cloud-init e configurazione del kernel

**Cloud-init** elabora script (Cloud Config/shell/testo per Linux; PowerShell/CLI/testo per Windows) per configurare al primo avvio: impostazioni paese, hostname, chiavi SSH, utenti, password, repository, pacchetti. La **configurazione del kernel** serve per ottimizzare (ripartizione memoria, I/O scheduler, scheduler processi) e si fa via bootloader (append), via **sysctl** o ricompilando.

## Articoli correlati

- [[server-linux]]
- [[linux-gestione-sistema]]
- [[linux-filesystem-storage]]
- [[cloud-computing]]

## Fonti

- `raw/2 - ServerLinux_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server Linux", sezioni installazione, partizioni/LVM, boot, init/systemd, arresto, cloud-init.
