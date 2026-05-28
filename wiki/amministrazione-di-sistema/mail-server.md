---
tags: [mail, smtp, imap, pop3, postfix, spam, antivirus, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/3 - Servizi_COMPILED.pdf
---

# Mail Server

Il servizio di posta elettronica è composto da più componenti cooperanti: MTA per il trasporto (SMTP), MDA per la consegna locale, e server IMAP/POP3 per l'accesso remoto alle mailbox. È uno dei vettori di attacco più sfruttati (>50% degli attacchi via email) e richiede attenzione speciale a spam, virus e autenticità del mittente.

## Punti chiave

- **Architettura a strati**: MUA (client) → MTA (SMTP, porta 25) → MDA (consegna locale) → mailbox → POP3/IMAP/webmail.
- **Formato MIME**: estende il testo ASCII a contenuti binari (allegati, HTML, multimedia); ogni mail ha header + uno o più body.
- **MTA**: Postfix (raccomandato), Sendmail (storico, monolitico, vulnerabile), Qmail, Exim; su Windows: Exchange.
- **Anti-spam**: filtri bayesiani, RBL (blacklist), SpamAssassin; autenticazione mittente con SPF, DKIM, DMARC.
- **Anti-virus**: ClamAV (open source) o soluzioni commerciali, integrate via Amavis o MailScanner.
- **Sicurezza trasmissione**: SMTP su TLS (STARTTLS), IMAP/POP3 su SSL; S/MIME o PGP per cifratura end-to-end.
- **Formato mailbox**: `mbox` (un file per casella) vs `Maildir` (un file per messaggio) — Maildir preferito per concorrenza IMAP.

---

## Architettura del sistema di posta

```
[MUA client]
    │ SMTP (submission, port 587 o 465)
    ▼
[MTA mittente]  ←── cerca MX record del destinatario via DNS
    │ SMTP (port 25)
    ▼
[MTA destinatario] ─── MDA ──→ [Mailbox (mbox/Maildir)]
                                    │
                          POP3 (110) o IMAP (143)
                                    │
                              [MUA destinatario]
                          oppure Webmail (HTTPS)
```

### Componenti

| Componente | Funzione | Esempi |
|-----------|---------|--------|
| **MUA** (Mail User Agent) | Client di posta dell'utente | Thunderbird, Outlook, Apple Mail |
| **MTA** (Mail Transport Agent) | Trasporto SMTP server-to-server | Postfix, Sendmail, Exim, Exchange |
| **MDA** (Mail Delivery Agent) | Consegna nella mailbox locale | Procmail, `mail.local`, Dovecot LDA |
| **Mailbox** | Storage dei messaggi | Formato mbox o Maildir |
| **IMAP/POP3 server** | Accesso remoto alle mailbox | Dovecot, Cyrus, UW-IMAP |

---

## Formato di una email

### Struttura

```
[Header]
From: mittente@esempio.it
To: destinatario@altro.it
Subject: Oggetto del messaggio
Date: Wed, 28 May 2026 10:00:00 +0200
MIME-Version: 1.0
Content-Type: multipart/alternative; boundary="abc123"
Message-ID: <uuid@server>

[Body - parte 1: text/plain]
Testo del messaggio

[Body - parte 2: text/html]
<html>Testo HTML</html>
```

- Gli header `Received:` mostrano il percorso del messaggio attraverso i server.
- **MIME** (Multipurpose Internet Mail Extensions): permette allegati, HTML, caratteri non-ASCII codificando con Base64 o Quoted-Printable.
- Il set di caratteri base è US-ASCII 7-bit; esteso con UTF-8 via MIME.

### Formati mailbox

| Formato | Struttura | Pro | Contro |
|---------|----------|-----|--------|
| **mbox** | Un file per casella; messaggi concatenati con `From ` come separatore | Semplice, backup facile | Accesso concorrente problematico; file cresce indefinitamente |
| **Maildir** | Una directory per casella; un file per messaggio (cur/new/tmp) | Accesso concorrente sicuro; backup incrementale semplice | Molti file piccoli, overhead filesystem |
| **Database** | Pseudo-DB (Cyrus) o RDBMS | Ricerche veloci, quota granulare | Complessità, vendor lock-in |

---

## SMTP

### Protocollo

**Simple Mail Transfer Protocol** — porta **25** (server-to-server), 587 (submission client autenticato), 465 (SMTPS, obsoleto ma diffuso).

Scambio testuale:
```
S: 220 mail.esempio.it ESMTP Postfix
C: EHLO client.esempio.it
S: 250-mail.esempio.it ... 250 STARTTLS
C: STARTTLS
[handshake TLS]
C: MAIL FROM:<mittente@esempio.it>
C: RCPT TO:<dest@altro.it>
C: DATA
C: [headers + body]
C: .
S: 250 OK
C: QUIT
```

### Open relay vs closed relay

- **Open relay**: accetta email da qualsiasi sorgente e le recapita a qualsiasi destinazione → sfruttato dagli spammer → inserito nelle RBL.
- **Closed relay**: accetta email solo da sorgenti autorizzate (reti proprie, utenti autenticati via SMTP AUTH).
- **Alternativa a IP-based relay**: SMTP AUTH (porta 587) + Pop Before SMTP (obsoleto).

---

## MTA: Postfix

### Architettura

Postfix è modulare (il contrario di Sendmail monolitico): ogni componente è un demone separato con privilegi minimi, coordinati dal master daemon.

```
[internet/local] → smtpd → cleanup → incoming queue
                                   → active queue → qmgr → smtp (outbound)
                                                          → local (delivery)
                                                          → pipe (MDA)
```

### Configurazione base (`/etc/postfix/main.cf`)

```
myhostname = mail.esempio.it
mydomain = esempio.it
myorigin = $mydomain
mydestination = $myhostname, localhost.$mydomain, $mydomain
inet_interfaces = all
mynetworks = 127.0.0.0/8, 192.168.0.0/16
relay_domains =
relayhost =                    # consegna diretta a Internet
# relayhost = [smtp.isp.com]  # oppure via smarthost provider
```

### Comandi utili

```bash
postfix check          # verifica configurazione
postfix reload         # rilegge configurazione
mailq                  # lista coda in uscita
postsuper -d ALL       # svuota coda
postcat -q ID          # visualizza messaggio in coda
postmap /etc/postfix/virtual  # rebuild tabelle
```

### Code

| Coda | Path | Contenuto |
|------|------|-----------|
| Maildrop | `/var/spool/postfix/maildrop` | Mail locali in ingresso |
| Incoming | `/var/spool/postfix/incoming` | Dopo cleanup, pronte per delivery |
| Active | `/var/spool/postfix/active` | In consegna attiva |
| Deferred | `/var/spool/postfix/deferred` | Temporaneamente non consegnabili |

---

## Alias e forward

### Alias (`/etc/aliases`)

```
# Alias fondamentali (sempre presenti)
MAILER-DAEMON: postmaster
postmaster:    root
abuse:         root

# Alias utente
mario.rossi:   mrossi
supporto:      mrossi, lbianchi

# Lista di distribuzione via file
team:          :include:/etc/postfix/team-list
```

Aggiornare con: `newaliases` (Sendmail/Postfix) o `postalias /etc/aliases`.

### Forward personale (`~/.forward`)

```
# Inoltra una copia all'indirizzo esterno e salva localmente
\mrossi
mario.rossi@gmail.com
```

---

## Anti-Spam

### Tipologie di filtro

| Tipo | Cosa analizza | Strumenti |
|------|--------------|-----------|
| **Connection filtering** | IP sorgente, reputazione | RBL (Realtime Blackhole List), greylisting |
| **Sender filtering** | Header From/Envelope-From, SPF | SPF check |
| **Recipient filtering** | Destinatario esiste? | Verifica LDAP/DB |
| **Content filtering** | Corpo del messaggio, allegati | SpamAssassin, filtri bayesiani |

### Autenticazione del mittente

| Standard | Meccanismo | Dove |
|----------|-----------|------|
| **SPF** (RFC 7208) | Record TXT DNS che elenca i server autorizzati a inviare per il dominio | DNS del mittente |
| **DKIM** | Firma crittografica degli header con chiave privata; chiave pubblica nel DNS | Header email + DNS |
| **DMARC** | Politica su cosa fare quando SPF/DKIM falliscono (none/quarantine/reject); reports | DNS del mittente |
| **ARC** | Catena di autenticazione per email inoltrate (risolve problemi DMARC con mailing list) | Header email |

Esempio record SPF:
```
v=spf1 mx a:mail.esempio.it ip4:1.2.3.4 ~all
```

### Greylisting

Alla prima connessione da un server sconosciuto, il MTA risponde con `451 Try later`. I server legittimi riprovano; molti spambot no. Semplice ed efficace ma introduce ritardo alla prima email.

### SpamAssassin

Sistema di scoring modulare: assegna punteggi per regole varie (header sospetti, RBL, Razor, filtri bayesiani); email sopra soglia etichettata come spam.

---

## Anti-Virus

| Componente | Funzione |
|-----------|---------|
| **ClamAV** | Engine antivirus open source; database aggiornato via `freshclam` |
| **Amavis** | Interfaccia tra MTA e scanner antivirus/antispam; supporta ClamAV + SpamAssassin |
| **MailScanner** | Alternativa ad Amavis; gestisce code doppie |
| Soluzioni commerciali | Symantec, McAfee, Sophos |

Integrazione tipica: `Postfix → Amavis → ClamAV + SpamAssassin → Postfix → consegna`.

**Problema contenuti**: archivi multivolume, file passwordati, JavaScript — difficili da analizzare; policy di blocco estensioni pericolose.

---

## POP3 e IMAP

### POP3 (Post Office Protocol v3)

- Porta **110** (o 995 con SSL).
- Scarica i messaggi dal server (opzionale: cancella dopo download).
- Accesso **non concorrente**: una sola connessione per mailbox alla volta.
- Adatto a client unici senza sincronizzazione.

```
telnet mail.esempio.it 110
+OK ...
USER mario
+OK
PASS password
+OK logged in
LIST
RETR 1
DELE 1
QUIT
```

### IMAP (Internet Message Access Protocol v4)

- Porta **143** (o 993 con SSL).
- I messaggi restano sul server; il client sincronizza lo stato.
- Accesso **concorrente**: più client possono accedere alla stessa mailbox.
- Supporta cartelle, ricerca server-side, flag (letto/risposto/contrassegnato).
- Indispensabile per accesso multi-dispositivo.

### Confronto POP3 vs IMAP

| Aspetto | POP3 | IMAP |
|---------|------|------|
| Messaggi sul server | No (default) | Sì |
| Multi-device | Difficile | Nativo |
| Accesso concorrente | No | Sì |
| Ricerca server-side | No | Sì |
| Cartelle sul server | No | Sì |
| Uso tipico | Client singolo, archiviazione locale | Webmail, smartphone, sync |

### Server IMAP/POP3

| Software | Note |
|---------|------|
| **Dovecot** | Moderno, performante, supporta Maildir e mbox; consigliato |
| **Cyrus IMAP** | Formato proprietario ad alte prestazioni; usato da grandi ISP |
| **Courier IMAP** | Incluso nel Courier MTA |
| **UW-IMAP** | University of Washington; storico, meno usato oggi |

---

## Sicurezza e cifratura

### Canale di trasporto

- **STARTTLS**: upgrade da SMTP plaintext a TLS sulla stessa connessione (porta 25/587).
- **SMTPS**: SMTP su TLS sin dall'inizio (porta 465).
- **IMAPS / POP3S**: IMAP/POP3 su SSL (porte 993/995).

### Cifratura end-to-end

- **PGP/GPG**: mittente firma con chiave privata, cifra con chiave pubblica del destinatario; scambio chiavi manuale.
- **S/MIME**: cifratura email basata su certificati X.509; integrata in Outlook, Apple Mail.

---

## Groupware e soluzioni enterprise

Un **sistema groupware** integra posta, calendario, rubrica, instant messaging e gestione progetti.

| Sistema | Piattaforma | Note |
|---------|------------|------|
| **Microsoft Exchange** | Windows Server | Standard enterprise; usa MAPI+HTTP, EWS; supporta IMAP/POP3 |
| **Exchange Online** (Microsoft 365) | SaaS | Nessuna infrastruttura da gestire |
| **Google Workspace** | SaaS | Gmail, Calendar, Drive integrati |
| **Dovecot + Postfix** | Linux | Soluzione open source scalabile |
| **Lotus/HCL Domino** | Linux/Windows | Mercato storico enterprise |
| **Zimbra** | Linux | Groupware open source con webmail completa |

### Microsoft Exchange — caratteristiche chiave

- Formato mailbox proprietario (JET database → Exchange store).
- Protocolli: MAPI over HTTP (Outlook), EWS (Exchange Web Services), ActiveSync (mobile), OWA (webmail).
- Differenza Standard vs Enterprise: dimensione Datastore (16/75 GB vs 8 TB), numero datastore (1 vs 20).
- Richiede Active Directory e Windows Server.

---

## Virtual hosting mail

- Più domini di posta sullo stesso server: ogni zona DNS punta il suo MX record allo stesso server.
- I messaggi vengono smistati in code o directory separate per dominio.
- Gli utenti possono essere virtualizzati (non servono account di sistema per ogni casella) tramite LDAP, database o file map.

Esempio Postfix:
```
virtual_mailbox_domains = esempio.it, altro.it
virtual_mailbox_base = /var/mail/vhosts
virtual_mailbox_maps = hash:/etc/postfix/vmailbox
```

---

## Log e monitoraggio

Log Postfix (via syslog):
```
May 28 10:00:01 mail postfix/smtpd[1234]: connect from unknown[1.2.3.4]
May 28 10:00:02 mail postfix/qmgr[1235]: ABC123: from=<mitt@esempio.it>, size=1024, nrcpt=1
May 28 10:00:03 mail postfix/smtp[1236]: ABC123: to=<dest@altro.it>, relay=mx.altro.it, status=sent
```

Strumenti analisi: `pflogsumm` (report giornaliero Postfix), `mailgraph` (grafici).

---

## Articoli correlati

- [[dns-server]]
- [[web-server]]
- [[ftp-server]]
- [[servizi-infrastrutturali]]
- [[linux-log-monitoraggio-rete]]
- [[server-windows]]

## Fonti

- `raw/3 - Servizi_COMPILED.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
