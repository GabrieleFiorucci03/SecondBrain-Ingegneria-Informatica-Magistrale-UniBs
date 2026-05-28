---
tags: [amministrazione-di-sistema, linux, log, syslog, monitoraggio, rete, firewall]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - ServerLinux_COMPILED.pdf
---

# Log, monitoraggio e rete in Linux

Logging, monitoraggio e configurazione di rete sono attività di run-time fondamentali per mantenere un server Linux affidabile e sicuro. I log registrano gli eventi, il monitoraggio copre ciò che i log non dicono, la rete connette il server ai servizi.

## Punti chiave

- **Log** in `/var/log/*`: accessi utenti/root, errori di sistema/servizi, messaggi del kernel — ma non carico CPU/disco. Rotazione con **logrotate** (non backup). Il demone **syslogd** smista i log (`/etc/syslog.conf`, può inviare a email/macchine remote); gli accessi sono gestiti da login (`/var/run/utmp`, `/var/log/wtmp`, `/var/log/lastlog`).
- **Formato syslog** (RFC 3164): `<PRI> TIMESTAMP HOSTNAME TAG CONTENT`. `PRI = FACILITY*8 + SEVERITY`; facility 0-23 (kern, user, mail, daemon, auth...), severity 0-7 (emerg, alert, crit, error, warning, notice, info, debug).
- Con **systemd** i log sono gestiti da **journald** (`journalctl -xd`); syslog continua a essere usato in parallelo.
- **Monitoraggio**: i log non danno tutto (uso CPU/disco/rete). Script custom; comandi `logger`, `vmstat`, `sar`; programmi MRTG, RRD.
- **Configurazione rete**: interfaccia (driver scheda, bond/aggregato, interfaccia virtuale), assegnazione IP (`ifconfig`), routing (`route add`, default gateway). DNS lato client: resolv statico (`/etc/hosts`) o dinamico (`/etc/resolv.conf`, framework resolvconf). Comando moderno **ip** (`ip address show`, `ip route show`).
- **Predictable Network Interface Names**: `eno1` (index BIOS), `ens1` (PCIe hotplug slot), `enp2s0` (posizione fisica), `enx...` (MAC), vs classico `eth0`. Gestori: **Netplan** (Ubuntu, YAML), **NetworkManager** (RedHat).

## Strumenti di rete

- **Open vSwitch**: virtual switch multilayer open source (Apache 2.0), usato al posto del bridge (non del bond), nella virtualizzazione e nell'**SDN** (VLAN isolation, traffic filtering, QoS, OpenFlow).
- Comandi: controllo parametri (`ifconfig`, `route`), diagnostica (`ping`, `traceroute`, `nslookup`, `tcpdump`), client carattere (`mail`, `lynx`/`links`, `wget`, `ftp`/`ncftp`).
- **Terminali remoti**: protocolli insicuri (`telnet`, `rlogin`, `rsh`, `rexec`, `rcp`) sostituiti dal protocollo sicuro **ssh** (anche al posto di FTP), con redirezione I/O e autenticazioni multiple (utente/IP remoto, chiavi digitali). Gestione remota sicura: niente SSH1, MAC forti, disabilitare login root/password, chiavi SSH, limiti tentativi, rete di management separata.

## Firewall e internals

- **Firewall**: storicamente **iptables**, oggi spesso **firewallD** (`firewall-cmd --list-all-zones`, `firewall-cmd --add-service=dns --zone=public --permanent`).
- **Inside the kernel**: sorgenti suddivisi in parti (SCI, PM, VFS, MM, Network Stack, Arch, Device Drivers); filesystem virtuali `/proc`, `/sys`, `/dev` (udev).
- **Linux performance tools** mappati sullo stack: applicazioni/syscall (`strace`, `ltrace`, `perf`), CPU (`top`, `mpstat`, `pidstat`), memoria (`vmstat`, `free`, `slabtop`), I/O (`iostat`, `iotop`, `blktrace`), rete (`netstat`, `ss`, `tcpdump`, `iptraf`, `nicstat`), vari (`sar`, `dstat`, `/proc`).

## Articoli correlati

- [[server-linux]]
- [[linux-gestione-sistema]]
- [[linux-utenti-autenticazione]]
- [[sicurezza-di-sistema]]

## Fonti

- `raw/2 - ServerLinux_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server Linux", sezioni log/syslog/journald, monitoraggio, rete, Open vSwitch, terminali remoti, firewall, performance.
