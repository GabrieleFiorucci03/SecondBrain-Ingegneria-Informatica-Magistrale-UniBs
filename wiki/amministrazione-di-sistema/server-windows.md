---
tags: [windows, server, amministrazione-di-sistema, nt, active-directory, powershell, ntfs, refs, smb]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - ServerWindows_COMPILED.pdf
---

# Server Windows

Windows Server è la famiglia di sistemi operativi Microsoft per ambienti server, basata sul kernel NT (New Technology) sviluppato a partire dal 1988. Copre ambiti che vanno da piccole aziende fino a datacenter enterprise, con versioni differenziate per dimensione e funzionalità.

## Punti chiave

- **Radici in NT**: l'architettura NT nasce da Dave Cutler (ex DEC/VMS); tutti i Windows moderni (client e server) condividono lo stesso kernel NT, con tuning e servizi specifici.
- **Ciclo di rilascio LTSC/SAC**: le versioni Long-Term Servicing Channel hanno 5+5 anni di supporto; il canale semestrale (SAC, ora dismesso) offriva solo 18 mesi.
- **Edizioni**: Essential (piccole realtà), Standard (ambienti non/poco virtualizzati), Datacenter (alta virtualizzazione + Storage Spaces Direct), Azure Edition.
- **Gestione basata su ruoli e feature**: da WS 2008 ogni servizio è un Ruolo (o Feature), con autoconfigurazione di firewall e dipendenze.
- **PowerShell come shell primaria**: sostituisce cmd.exe e cscript; basato su .NET, opera su oggetti (non testo); multipiattaforma con PowerShell Core.
- **NTFS e ReFS**: NTFS è il filesystem nativo (ACL, cifratura EFS, shadow copy, journaling); ReFS (da WS 2012) scala a 1 YB di volume con integrità strutturale avanzata.
- **SMB 3.x**: protocollo di condivisione file con crittografia, multicanale, failover trasparente e supporto RDMA.

---

## Storia di Windows NT

| Anno | Evento |
|------|--------|
| 1970 | Progetto VMS presso DEC (Dave Cutler) |
| 1988 | Microsoft ingaggia Cutler per un nuovo SO (Windows NT) |
| 1993 | Windows NT 3.1 e NT Advanced Server 3.1 (5 anni, >100 M$) |
| 1996 | NT 4.0: più licenze server di Unix; GUI analoga a Win 95 |
| 2000 | Windows 2000 (NT 5.0); mancata confluenza SO Microsoft |
| 2002 | Confluenza: WinXP (NT 5.1), Server 2003 (NT 5.2) |
| 2007-2013 | Famiglie NT 6.0–6.3 (Vista/7/8/8.1 + Server 2008/2012/2012 R2) |
| 2015-2016 | Famiglia NT 7: Windows 10 + Windows Server 2016 |
| 2019-2025 | WS 2019 (Redstone), WS 2022 (Iron), WS 2025 |

---

## Versioni e Lifecycle

### Ciclo di rilasci

- **LTSC (Long-Term Servicing Channel)**: nuova versione principale ogni 2–3 anni; 5 anni mainstream + 5 anni extended.
- **SAC (Semi-Annual Channel)**: rilasci ogni 6 mesi, supporto 18 mesi, solo Server Core (no GUI).

### Lifecycle principali versioni

| Versione | Inizio | Fine Mainstream | Fine Extended |
|----------|--------|-----------------|---------------|
| WS 2008 | 2008 | 2015-01-13 | 2020-01-14 |
| WS 2012 / R2 | 2012/2013 | 2018-10-09 | 2023-10-10 |
| WS 2016 | 2016-10-15 | 2022-01-11 | 2027-01-12 |
| WS 2019 | 2018-11-13 | 2024-01-09 | 2029-01-09 |
| WS 2022 | 2021-11-02 | 2026-10-13 | 2031-10-14 |

---

## Edizioni

### WS 2016/2019 Editions

| Edizione | Target |
|----------|--------|
| **Essential** | Piccole aziende, max 2 CPU, 25 utenti |
| **Standard** | Ambienti non virtualizzati o leggermente virtualizzati |
| **Datacenter** | Cloud e ambienti ad alta virtualizzazione |
| **Hyper-V Server** | Solo hypervisor, gratuito |

### WS 2022 Editions — differenze chiave

| Feature | Standard | Datacenter | Datacenter Azure Ed. |
|---------|----------|------------|----------------------|
| Storage Spaces Direct | No | **Sì** | **Sì** |
| Software-defined Networking | No | **Sì** | **Sì** |
| Storage Replica | Limitato (1 partnership, 2 TB) | Illimitato | Illimitato |
| SMB over QUIC | No | No | **Sì** |
| Hotpatching | No | No | **Sì** |
| Azure Extended Network | No | No | **Sì** |

---

## Caratteristiche dei Server Windows

- **Affidabilità**: migliorata nel tempo; casi residui di BSOD (Blue Screen of Death).
- **Scalabilità**: buona; supporto SMP nativo senza ricompilazione del kernel.
- **Prestazioni**: generalmente buone, ma con requisiti hardware di medio-alto livello.
- **Manutenibilità**: strumenti per la gestione centralizzata (MMC, Server Manager, Admin Center).
- **Licenze**: costi su licenza server + CAL (Client Access License) per ogni client.
- **Driver**: buona disponibilità (Windows Driver Model – WDM); Hardware Compatibility List (HCL).
- **Portabilità**: IA-32, x64, IA-64, ARM (da WS 2019).
- **Sicurezza**: servizi minimi di default, firewall integrato (da WS 2003 SP1), Secure Boot, VBS.
- **Localizzazione**: supporto Unicode, distribuzioni per ogni lingua.

---

## Architettura NT

L'architettura NT è stratificata in **User Mode (ring 3)** e **Kernel Mode (ring 0)**.

### User Mode
- **Processi di supporto**: Logon, Session Manager (Smss.exe), Winlogon.exe, LSASS.EXE.
- **Sottosistemi ambientali**: Win32, POSIX.
- **Processi di sistema**: Spooler, Task Manager.
- **Applicazioni utente**.
- Comunicano con il kernel tramite le **DLL dei sottosistemi**.

### Kernel Mode
- **Hardware Abstraction Layer (HAL)**: astrae l'hardware specifico; permette la portabilità su diverse architetture.
- **Kernel**: scheduling, sincronizzazione, interrupt.
- **Device Drivers**: driver hardware.
- **Executive**: I/O manager, Memory manager, Process manager, Security monitor, ecc.
- **Supporto User e GDI**: finestre, messaggi e grafica (nel kernel dal NT 4.0).

### Client vs Server: differenze di tuning

| Parametro | Workstation | Server |
|-----------|-------------|--------|
| Cache write-throttling | Ripulisce più spesso | Mantiene più a lungo |
| Paginazione | Normale | Parti di SRV.SYS non paginabili |
| Max processori/RAM | Minore | Maggiore |
| Connessioni di rete concorrenti | Limitate | Elevate |
| Thread di sistema | Meno | Più |

---

## Opzioni di Installazione

### Server Core
Modalità minimale introdotta con WS 2008: nessuna GUI shell, solo interfaccia a riga di comando. Riduce l'attack surface e il consumo di risorse.

### Nano Server
Versione ultra-ridotta (20× più piccola di Server Core) introdotta con WS 2016. Pensata per container e microservizi; ha rimosso il supporto a molti ruoli tradizionali.

### Gerarchia delle modalità

```
Nano Server (minimo) < Server Core < Minimal Server Interface < Full GUI Shell
```

---

## Installazione di Windows Server

**Passi fondamentali**:
1. Scelta della destinazione: partizionamento e tipo di filesystem.
2. Impostazione rete.
3. Impostazione password Administrator.
4. Attivazione licenza (Windows Product Activation — WPA).

**Attivazione**:
- **WPA**: Product Key (25 caratteri) + Installation ID (dipende dall'hardware) → Confirmation ID da Microsoft.
- **KMS (Key Management Service)**: per ambienti multilicenza con un server di attivazione interno.
- **MAK (Multiple Activation Key)**: chiave singola per multiple installazioni.
- **Validazione**: Windows Genuine Advantage (WGA) verifica l'autenticità.

### Scelta del Filesystem

| Tipo | Max partizione | Max file | Cluster tipico | Note |
|------|---------------|----------|----------------|------|
| FAT16 | 2 GB | 4 GB | 2–32 KB | Obsoleto |
| FAT32 | 32 GB consigliati | 4 GB | 4–32 KB | Obsoleto per server |
| **NTFS** | 256 TB | 16 TB | 512 B – 4 KB | Standard server |
| **ReFS** | 1 YB | 16 EB | Fino a 64 KB | Da WS 2012 |

---

## Boot del Sistema

### Boot BIOS/IA-32 (legacy)
1. POST (Power On Self Test).
2. BIOS cerca bootloader su MBR (512 byte) → caricato a 0x700h.
3. **NTLDR** (NT Loader) carica Ntdetect.com, legge `boot.ini`, carica il kernel.

**File chiave NTLDR**: `Bootsect.dos`, `Ntdetect.com`, `Boot.ini`.

**ARC path** in `boot.ini`:
```
[boot loader]
timeout=30
default=multi(0)disk(0)rdisk(0)partition(1)\Windows
[operating systems]
multi(0)disk(0)rdisk(0)partition(1)\Windows="WS2003" /fastdetect
```

### Bootstrap di NT
Il kernel (`Ntoskrnl.exe`) + HAL (`Hal.dll`) + driver video (`Bootvid.dll`) vengono caricati da NTLDR.

**Sequenza Session Manager (Smss.exe)**:
1. Verifica disco (AUTOCHK).
2. Carica pagefile.
3. Avvia Winlogon.exe → LSASS.EXE (autenticazione) + SPOOLSS.EXE (stampa).
4. Avvia Services Controller (Screg.exe) → carica tutti i servizi e driver.

### Boot NT 6.x (Vista/2008+)
Nuovo bootloader che sostituisce NTLDR. Configurazione in **BCD (Boot Configuration Data)** — database binario editabile con `bcdedit.exe`. Da WS 2008 R2: possibilità di boot da disco virtuale **VHD**.

---

## Registro di Sistema

Database gerarchico di configurazione (`regedit.exe`). Memorizzato in `%systemroot%\system32\config`.

| Chiave | File su disco |
|--------|---------------|
| HKEY_LOCAL_MACHINE\SAM | Sam, Sam.log |
| HKEY_LOCAL_MACHINE\Security | Security, Security.log |
| HKEY_LOCAL_MACHINE\Software | Software, Software.log |
| HKEY_LOCAL_MACHINE\System | System, System.alt |
| HKEY_USERS\.DEFAULT | Default, Default.log |
| HKEY_CURRENT_USER | Ntuser.dat, Ntuser.dat.log |

Protetto da ACL; backup tramite `regback.exe` o NTbackup/System State.

---

## Gestione del Sistema

### Hardware
- **Device Manager** (`devmgmt.msc`): driver, profili hardware, `driverquery`.
- **Windows Driver Model (WDM)**: driver "certificati" Microsoft; HCL per compatibilità.

### Software
Distribuzione: pacchetti auto-installabili, **MSI** (`.msi`) o **MSIX** (`.msix`).

Aggiornamento: **Windows Update** → **Microsoft Update** → **WSUS (Windows Server Update Services)** per gestione centralizzata in azienda.

### Processi
- `tasklist` da riga di comando.
- **Task Manager**: visualizza processi/thread, memoria, permette modifica priorità e terminazione.
- Strumenti terze parti: **Sysinternals** (Process Explorer, ecc.).

### Servizi
- GUI: `services.msc` (snap-in MMC); modalità avvio: automatico, manuale, disabilitato.
- CLI: `net start Servizio` / `net stop Servizio`.
- Dipendenze tra servizi configurabili; modalità di ripristino in caso di crash.

**Servizi da disabilitare in un server** (principio minimo privilegio): Alerter, ClipBook, Indexing Service, Messenger, Remote Registry, Background Intelligent Transfer Service, ecc.

### Clock
- **W32Time** (Windows Time Synchronization Service): implementa SNTP.
- Comandi: `net time \\TIMESERVER /set /y`, `net time /setsntp:NTPSERVER`.

### Gestione degli utenti
Attributi: ID accesso, password, gruppi, politiche account, cartella home, accesso remoto, diritti e privilegi.

**Quota disco**: disponibile da NT 5.0 su volumi NTFS; ogni utente ha una "porzione" di disco.

---

## Sicurezza e Autenticazione

### Security Accounts Manager (SAM)
Database locale degli account e degli hash delle password:
- Posizione: `HKEY_LOCAL_MACHINE\SAM` + `%systemroot%\system32\config\SAM`.
- Sui Domain Controller è sostituito da **Active Directory**.
- Crittografato con chiave di startup; gestito con `syskey.exe`.

### Security Identifier (SID)
Identificatore univoco per ogni account (utente, gruppo, macchina, servizio):
- Formato: `S-1-5-21-D1-D2-D3-RID`.
- **RID (Relative Identifier)**: parte univoca, simile a UID/GID Unix.
- SID predefiniti (well-known): `S-1-1-0` (Everyone), `S-1-5-18` (System), `S-domain-500` (Administrator), `S-domain-501` (Guest).

### Account particolari

| Account | Caratteristiche |
|---------|----------------|
| Administrator | Non eliminabile, non bloccabile, non disabilitabile, non rimovibile da Administrators group; rinominabile |
| Guest | Account ospite con privilegi minimi |
| LocalSystem | Accesso locale completo + accesso di rete limitato (nome macchina) |
| Network Service | Accesso locale limitato + accesso rete limitato |
| Local Service | Accesso locale limitato + accesso rete anonimo |

### Tipi di Autenticazione

| Tipo | Meccanismo |
|------|-----------|
| Locale | SAM |
| Dominio NT | NTLM + SAM |
| Active Directory | Kerberos V5 + AD |

Standard supportati: Kerberos V5, SSL/TLS, NTLM, Digest, Passport.

### Profili utente

| Tipo | Percorso | Caratteristiche |
|------|----------|----------------|
| Local | `%Systemdrive%\Users\%Username%` | Solo locale |
| Roaming | Server di file share | Si sincronizza al login/logout |
| Mandatory | Server (NTUSER.MAN) | Sola lettura; modifiche non persistono |
| Default User | Template per nuovi utenti | |

---

## Domini e Active Directory

### Workgroup vs Dominio
- **Workgroup**: ogni macchina ha il proprio SAM separato; nessuna gestione centralizzata.
- **Dominio**: un unico account utente in Active Directory, condiviso da tutte le macchine del dominio.

### Domini NT (legacy)
- **PDC (Primary Domain Controller)**: controller principale.
- **BDC (Backup Domain Controller)**: replica del PDC.
- Database utenti: SAM (limite 16.000 utenti).
- Relazioni di fiducia: gerarchiche (dominio principale) o paritarie (peer-to-peer).

### Domini Windows 2000+ (Active Directory)
Sostituisce il modello PDC/BDC con:
- **Foreste e alberi di domini**: struttura logica gerarchica.
- **Domain Controller**: server con AD installato (`dcpromo`).
- **Member Server**: server che offre servizi (file, print, web, ecc.) senza essere DC.
- **OU (Organizational Unit)**: suddivisione logica degli oggetti del dominio.
- **Group Policy**: applicazione di policy centralizzate a utenti e computer.

---

## Gestione Remota

### Modalità di gestione

| Tipo | Strumenti |
|------|-----------|
| Out of band | EMS (Emergency Management Services) su porta seriale |
| Remote Console CLI | PowerShell, WMI C, WinRS, SSH |
| Remote GUI | RDP (Remote Desktop), MMC, Server Manager, Windows Admin Center |
| Automated | Group Policy, RIS, Automated Setup |

### Windows Remote Management (WinRM)
- Basato su **WS-Management** (protocollo SOAP, firewall-friendly).
- Configurazione lato server: `winrm quickconfig`.
- **WinRS**: esegue comandi remotamente: `winrs -r:ServerName cmd.exe`.
- Alternativa: SSH server (nativo da WS 2019; chiavi disabilitate di default per Administrators) o PowerShell remoting (`New-PSSession`).

### Gestione Remota Sicura — Best Practice
- Usare RDP Gateway / Bastion Host.
- Disabilitare login diretti con account Administrator.
- Bloccare accessi con password vuota.
- Limitare i tentativi di login e impostare timeout di inattività.
- Rete di management separata; restrizioni IP; cambio porte default (opzionale).

### Remote Server Administration Tools (RSAT)
Pacchetto installabile su Windows 10/11 che include snap-in MMC e Server Manager per gestire server remoti senza installare i ruoli localmente.

### Windows Admin Center
Tool di gestione basato su browser (Project "Honolulu", 2018):
- Installabile su Windows 10/11 o WS 2016+.
- Non richiede RSAT, IIS, SQL o System Center.
- Supporta Edge e Chrome.
- Scala da singolo server a cluster.

### Azure Arc
Estende la gestione Azure (Azure Resource Manager, RBAC, sicurezza, compliance) a risorse on-premises, multi-cloud ed edge.

---

## Roles and Features

Introdotto con WS 2008:
- **Ruolo**: servizio principale (AD DS, DHCP, DNS, IIS, File Server, Hyper-V, ecc.).
- **Feature**: utility o componente di supporto (SNMP, Backup, Failover Clustering, ecc.).

Vantaggi: elenco chiaro delle funzionalità, gestione semplificata, autoconfigurazione di regole firewall e dipendenze tra pacchetti.

Gestione: Server Manager GUI o PowerShell (`Install-WindowsFeature`, `Get-WindowsFeature`).

---

## Windows PowerShell

Shell CLI e linguaggio di scripting orientato agli **oggetti** (non al testo), basato su .NET (ora .NET Core).

### Caratteristiche principali
- **Cmdlet**: comandi nel formato `Verbo-Sostantivo` (es. `Get-Process`, `Set-Service`).
- **Pipeline di oggetti**: i comandi si passano oggetti tipizzati, non stringhe; consente filtri e trasformazioni precise.
- **Espandibile**: moduli aggiuntivi (Exchange, AD, Storage, ecc.).
- **Multipiattaforma**: PowerShell Core gira su Linux e macOS.
- **Remoting**: `New-PSSession`, `Invoke-Command` per esecuzione remota.

### Comandi di base

```powershell
# Help
Get-Help Get-Disk
Get-Command -Noun Disk
Get-Command -Verb Get

# Esempio pipeline
Get-Process | Where { $_.handles -gt 500 } | Sort handles | Format-Table

# Gestione servizi
Get-Service | Where Status -eq Running
Start-Service -Name wuauserv
Stop-Service -Name wuauserv

# Remoting
New-PSSession -ComputerName Server01
Invoke-Command -ComputerName Server01 -ScriptBlock { Get-Disk }
```

---

## File System Windows

### NTFS (NT File System)
File system nativo di Windows, simile a un database transazionale:
- **ACL con ereditarietà**: controllo granulare per utenti/gruppi (Owner, Read, Write, Execute, Delete, change Permissions, take Ownership).
- **Nomi lunghi** (Unicode) + compatibilità 8.3.
- **Compressione** a livello di file/cartella.
- **Cifratura EFS** (Encrypting File System, da NT 5.0): accesso multi-utente allo stesso file.
- **Ridimensionamento dinamico** della partizione (da WS 2003).
- **Hot-fixing**: spostamento dinamico dei settori difettosi.
- **Journaling transazionale**: commit e roll-back per integrità in caso di crash.

### ReFS (Resilient File System)
Introdotto con WS 2012, progettato per scale-out e integrità:
- Volume massimo: **1 YB (YottaByte)**.
- File massimo: **16 EB (ExaByte)**.
- Metadati e file in **B+Tree** (struttura simile a database relazionale).
- Controllo di integrità integrato nei metadati.
- Operazioni efficienti: **Sparse VDL** (write-zero), **Block Cloning** (copia senza duplicare dati).
- Supporto deduplication da v3.2 (WS 2019 v1709).

### Storage Spaces
Introdotti con WS 2012:
- Astrazione dei dischi fisici in **Storage Pool** → **Virtual Disk** → **Volume**.
- Include funzionalità RAID software (mirroring, parity).
- **Storage Spaces Direct (S2D)**: solo Datacenter edition; abilita SAN software-defined con dischi locali aggregati via cluster.

---

## SMB (Server Message Block)

Protocollo Microsoft per la condivisione di file, cartelle e stampanti in rete.

### Versioni

| Versione | OS | Note |
|----------|----|------|
| CIFS | NT 4.0 (1996) | Precursore |
| SMB 1.0 | WS 2000–2003 R2 | Vulnerabile (EternalBlue); disabilitare |
| SMB 2.0 | Vista/WS 2008 | |
| SMB 2.1 | Win7/WS 2008 R2 | |
| SMB 3.0 | Win8/WS 2012 | Crittografia, multicanale, RDMA |
| SMB 3.x | WS 2016–2022 | SMB over QUIC (Azure Edition) |

### SMB 3.x — funzionalità principali
- **Failover trasparente**: le sessioni SMB sopravvivono al failover del nodo cluster.
- **SMB Multicanale**: utilizza più interfacce di rete simultaneamente per throughput e fault tolerance (abilitato di default su WS 2012+).
- **SMB Direct**: trasferimento dati via RDMA (Remote Direct Memory Access) per latenza minima.
- **Crittografia SMB**: protezione end-to-end (AES-128-CCM/AES-256-GCM).
- **SMB over QUIC**: accesso file sicuro via QUIC su Internet (solo Azure Edition).

### Condivisioni predefinite (amministrative)

| Condivisione | Scopo |
|-------------|-------|
| `ADMIN$` | Amministrazione remota del sistema (= %SYSTEMROOT%) |
| `C$`, `D$`, … | Accesso amministrativo ai volumi |
| `IPC$` | Comunicazioni inter-processo |
| `PRINT$` | Amministrazione stampanti remote |
| `NETLOGON` | Servizio Net Logon (solo domini) |

---

## Volume Shadow Copy Service (VSS)

Meccanismo per backup consistenti anche con file aperti:

| Componente | Ruolo |
|-----------|-------|
| VSS Service | Coordinamento tra le parti |
| VSS Requester | Richiede la creazione dello snapshot (tipicamente il backup software) |
| VSS Writer | Garantisce la consistenza dei dati dell'applicazione |
| VSS Provider | Crea e mantiene le shadow copy (sistema, hardware o software) |

**Shadow Copies of Shared Folders**: versioni precedenti di file nelle share di rete ("cestino con controllo versioni").

---

## Backup e Ripristino

### Strumenti
- **NTbackup**: tool standard fino a WS 2003; gestisce System State.
- **Windows Server Backup** (da WS 2008): incompatibile con NTbackup; usa VSS; supporta backup su volume o rete.
- **ASR (Automated System Recovery)**: ripristino bare-metal da CD di boot (tasto F2).

### System State
Insieme di dati critici per il ripristino:
- Registry locale, SYSVOL, COM+ registration database, Performance Counter Config.
- Active Directory, Certificate Services, DNS, Cluster Service (se presenti).

### Tipi di backup
- **Full (completo)**: copia tutto.
- **Incrementale**: solo file cambiati dall'ultimo backup (completo o incrementale); ripristino più lungo.
- **Differenziale**: solo file cambiati dall'ultimo full; compromesso tra spazio e velocità di ripristino.

### Modalità Rescue
Strumenti per emergenze (password persa, bootloader rotto, partizione corrotta):
- **Emergency Recovery Console** (installabile con `i386\winnt32.exe /cmdcons`).
- **CD Rescue**: Windows PE (Windows 2003 SP1 con limitazioni) o ERD Commander.
- Partizione di emergenza dedicata.

---

## Log e Monitoraggio

### Event Viewer (`eventvwr.msc`)
- Categorie: Sistema, Applicazione, Sicurezza, log dei singoli ruoli.
- I log sono memorizzati in `\Windows\System32\Config`.
- Troncamento automatico oltre una dimensione configurabile.
- Consultazione centralizzata da remoto tramite MMC.

### Monitor delle Prestazioni
- Completamente configurabile con contatori locali e/o remoti.
- Registra solo dopo l'attivazione esplicita del monitor.
- Integrato con il Task Manager per la visione in tempo reale.

---

## Sicurezza Avanzata

### Defender Firewall (Windows Firewall with Advanced Security)
- Tre profili: **Domain**, **Private**, **Public**.
- Regole in ingresso/uscita per programmi, porte, protocolli.
- Regole di connessione sicura (IPsec).
- Gestione tramite MMC snap-in o Group Policy.

### Virtualization-based Security (VBS)
Introdotto con WS 2016, potenziato in WS 2022:
- Usa l'hypervisor Hyper-V per isolare una porzione di memoria dal kernel.
- **Isolated User Mode (IUM)**: ambiente sicuro per processi sensibili (LSASS).
- **Windows Defender System Guard**: integrità del sistema al boot.
- **HVCI (Hypervisor-Protected Code Integrity)**: solo codice firmato nel kernel.
- **DRTM (Dynamic Root of Trust for Measurement)**: protezione firmware.
- **TPM 2.0**: hardware root-of-trust.

---

## Servizi TCP/IP

### Lato Server
- **DHCP Server**: assegnazione dinamica indirizzi IP.
- **DNS Server**: risoluzione nomi (DDNS integrato con AD).
- **WINS**: risoluzione nomi NetBIOS (obsoleto, sostituito da DNS in NT 5.0).
- **IIS (Internet Information Server)**: Web e FTP.
- **RAS (Remote Access Service)**: accesso remoto dial-up e VPN (PPTP, L2TP).
- **Active Directory Domain Services (AD DS)**: directory service per autenticazione centralizzata.

### Lato Client
- Client DHCP, WINS, RAS.
- Utilità diagnostiche: `arp`, `hostname`, `ipconfig`, `netstat`, `ping`, `route`, `tracert`.
- Utilità di connessione: telnet, ftp, rsh, finger.
- Supporto SNMP e stampa (`lpr`).

---

## Licenze

### Client Access License (CAL)
Ogni client che accede a risorse di un server NT deve avere una CAL:
- **Per server**: CAL valida solo per quel server (licenze simultanee).
- **Per device/user**: CAL valida per un utente/dispositivo su qualsiasi server NT.
- **Per seat** (pre-2003): CAL per client su qualsiasi server della LAN.

### Tipi di licenza prodotto
- **OEM**: legata all'hardware.
- **Retail/FPP**: acquisto singolo trasferibile.
- **Multilicenza**: Open License, Open Value, Select, Enterprise Agreement (acquisto); Open Value Subscription, EA Subscription (noleggio).
- **Software Assurance**: diritto agli upgrade durante la validità del contratto.

---

## Articoli correlati

- [[server-linux]]
- [[server]]
- [[linux-utenti-autenticazione]]
- [[linux-filesystem-storage]]
- [[linux-gestione-sistema]]
- [[linux-log-monitoraggio-rete]]

## Fonti

- `raw/2 - ServerWindows_COMPILED.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
