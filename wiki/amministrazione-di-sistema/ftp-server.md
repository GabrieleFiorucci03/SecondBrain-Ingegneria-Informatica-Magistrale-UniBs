---
tags: [ftp, sftp, file-transfer, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/3 - Servizi_COMPILED.pdf
---

# FTP Server

Il **File Transfer Protocol (FTP)** permette di accedere e trasferire file su una porzione del filesystem del server. Oggi FTP puro è considerato obsoleto per la mancanza di cifratura; le alternative sicure (SFTP via SSH, FTPS, WebDAV, HTTPS) sono preferite in tutti i contesti di produzione.

## Punti chiave

- **Due canali separati**: controllo su porta **21 TCP**, dati su porta **20 TCP** (modalità attiva) o porta casuale lato server (modalità passiva — necessaria dietro NAT/firewall).
- **In chiaro**: login e password trasmessi in chiaro → vulnerabile allo sniffing → usare SFTP o FTPS in produzione.
- **Tipi di utenti**: anonymous (accesso libero a `/home/ftp`), guest (password ma con chroot), real (utenti di sistema con accesso completo).
- **Chroot**: limitare l'utente FTP a una sotto-directory del filesystem, impedendo l'accesso a percorsi critici di sistema.
- **SFTP**: protocollo completamente diverso da FTP, basato su SSH; cifrato, non richiede porte aggiuntive (usa la porta SSH 22).
- **Alternative moderne**: SFTP, SCP, WebDAV, HTTPS/S3, strumenti cloud (SharePoint, Google Drive).

---

## Protocollo FTP

### Connessione e modalità

| Modalità | Canale dati | Note |
|----------|------------|------|
| **Attiva** (PORT) | Il server si connette al client sulla porta indicata | Problemi con firewall client-side |
| **Passiva** (PASV) | Il client si connette a una porta casuale sul server | Standard per client dietro NAT; richiede range porte aperto sul firewall server |

### Comandi principali

```
USER mario          → autenticazione
PASS password
LIST                → elenco directory
CWD /percorso       → cambia directory
PWD                 → directory corrente
RETR file.txt       → download
STOR file.txt       → upload
DELE file.txt       → cancella file
MKD cartella        → crea directory
RMD cartella        → rimuove directory
QUIT                → chiude sessione
```

---

## Tipi di utenti FTP

| Tipo | Accesso | Chroot | Autenticazione |
|------|---------|--------|----------------|
| **Anonymous** | Solo `/home/ftp` (o `/var/ftp/pub`) | Automatico | Nessuna (email come password per convenzione) |
| **Guest** | Area limitata | Sì, obbligatorio | Password; non sono utenti di sistema |
| **Real** | Intero filesystem (in base ai permessi Unix) | Opzionale | Password utente di sistema |

### Struttura area FTP anonima

```
/home/ftp/           ← root dell'area anonima (proprietà root, non scrivibile da ftp)
├── bin/             ← comandi minimi (ls, gzip) — necessari per il server FTP
├── etc/             ← file di sistema minimali
├── lib/             ← librerie per i comandi in bin/
└── pub/             ← directory pubblica scaricabile
```

Il server FTP esegue `chroot` automaticamente per gli utenti anonimi.

---

## Implementazioni Linux

| Software | Note |
|---------|------|
| **vsftpd** (Very Secure FTPd) | Leggero, sicuro, diffuso sulle distribuzioni moderne |
| **ProFTPD** | Configurazione stile Apache; molto flessibile |
| **Pure-FTPd** | Supporto utenti virtuali, quota, TLS |
| **Wu-FTPd** | Storico (Washington University); deprecato |

### Configurazione vsftpd di base (`/etc/vsftpd.conf`)

```
anonymous_enable=NO        # disabilita accesso anonimo
local_enable=YES           # abilita utenti locali
write_enable=YES           # permette upload
chroot_local_user=YES      # limita gli utenti alla home
chroot_list_enable=YES     # lista utenti esclusi dal chroot
chroot_list_file=/etc/vsftpd.chroot_list

ssl_enable=YES             # abilita FTPS
rsa_cert_file=/etc/ssl/certs/vsftpd.pem

pasv_min_port=40000        # range porte passive
pasv_max_port=50000

xferlog_enable=YES         # log trasferimenti
xferlog_file=/var/log/xferlog
```

### Modalità di avvio

- **Standalone**: `vsftpd` avviato come servizio (`systemctl start vsftpd`).
- **inetd/xinetd**: il demone viene attivato solo alla ricezione di una connessione (meno comune oggi).

---

## Sicurezza FTP

### Problemi principali

| Problema | Descrizione | Soluzione |
|----------|------------|----------|
| Credenziali in chiaro | Login/password intercettabili | FTPS (TLS) o passare a SFTP |
| Data channel in chiaro | File trasferiti non cifrati | FTPS o passare a SFTP |
| Bounce attack | Abuso del canale dati per proxy | Bloccare comando PORT verso IP terzi |
| Brute force | Tentativi di password ripetuti | fail2ban, limit tentativi, 2FA |
| Traversal | Utente accede fuori dalla sua area | chroot obbligatorio |

### FTPS (FTP over TLS)

Due modalità:
- **Explicit FTPS** (FTPES): si parte su porta 21 e si fa upgrade con `AUTH TLS`; più firewall-friendly.
- **Implicit FTPS**: connessione diretta su porta 990 già cifrata; meno supportato.

---

## Alternative a FTP

| Alternativa | Protocollo | Porte | Cifratura | Note |
|------------|-----------|-------|----------|------|
| **SFTP** | SSH File Transfer Protocol | 22 | Sì (SSH) | Non è FTP su SSH; protocollo completamente diverso |
| **SCP** | Secure Copy | 22 | Sì (SSH) | Solo trasferimento file, nessuna navigazione directory |
| **WebDAV** | HTTP(S) esteso | 80/443 | Con HTTPS | Montabile come disco di rete; supporto Windows nativo |
| **HTTPS upload** | HTTP POST/PUT | 443 | Sì (TLS) | CMS, portali web |
| **Object Storage** | S3-compatible API | 443 | Sì | MinIO, AWS S3, Azure Blob |
| **rsync** | rsync protocol o SSH | 873/22 | Con SSH | Sync efficiente (delta); usato per backup e deploy |

### SFTP — uso pratico

```bash
# Connessione interattiva
sftp utente@server

# Comandi SFTP (simili a FTP)
sftp> ls
sftp> get file.txt
sftp> put locale.txt /remoto/

# Trasferimento one-shot
scp file.txt utente@server:/percorso/
rsync -avz --progress /sorgente/ utente@server:/destinazione/
```

---

## Funzionalità avanzate

| Funzionalità | Descrizione |
|-------------|------------|
| **Log dei trasferimenti** | Ogni upload/download loggato in `/var/log/xferlog` (formato standard wu-ftpd) |
| **Quote disco** | Limite di spazio per utente o gruppo |
| **Compressione on-the-fly** | Compressione automatica dei file durante il trasferimento |
| **Messaggi di directory** | File `.message` o `.ftpwelcome` mostrati all'accesso |
| **Alias directory** | Percorsi virtuali che puntano a directory reali |
| **Utenti virtuali** | Account FTP non corrispondenti a utenti di sistema (via database, LDAP, file) |
| **Bandwidth throttling** | Limitazione della velocità di trasferimento per utente/ora |

---

## Articoli correlati

- [[web-server]]
- [[mail-server]]
- [[servizi-infrastrutturali]]
- [[linux-utenti-autenticazione]]
- [[linux-log-monitoraggio-rete]]

## Fonti

- `raw/3 - Servizi_COMPILED.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
