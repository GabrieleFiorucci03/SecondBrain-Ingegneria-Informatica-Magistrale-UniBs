---
tags: [dns, rete, servizi, bind, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/3 - Servizi_COMPILED.pdf
---

# DNS Server

Il **Domain Name System (DNS)** è il "database" distribuito e decentralizzato che risolve nomi di dominio in indirizzi IP (e viceversa). È il servizio più critico di Internet: senza DNS, quasi nessun altro servizio funziona. Introdotto nel 1987 (RFC 1035), progettato senza sicurezza — lacuna colmata progressivamente con DNSSEC, DoH, DoT.

## Punti chiave

- **Struttura gerarchica**: radice (`.`), TLD (`.com`, `.it`), SLD (`google.com`), subdomain; ogni livello delegato a name server autoritativi.
- **13 root server logici** nel mondo (lettere A–M, con anycast → centinaia di istanze fisiche).
- **Record types**: SOA, NS, A, AAAA, PTR, MX, CNAME, TXT, SRV, CAA — ognuno con scopo specifico.
- **TTL e caching**: ogni RR ha un TTL che controlla quanto i resolver lo mantengono in cache; TTL basso = aggiornamenti rapidi ma più query; TTL alto = meno carico ma propagazione lenta.
- **Primary/Secondary**: zone replicate da master a slave via zone transfer; il serial number nel SOA triggera la sincronizzazione.
- **BIND** è il server DNS più diffuso (ISC), composto da `named`, libreria resolver, tool di diagnostica.
- **Sicurezza**: DNSSEC firma crittograficamente i record; DoH e DoT cifrano le query sul canale.

---

## Struttura del namespace

```
. (root, livello 0)
├── com  (TLD)
│   ├── google  (SLD)
│   │   └── www  (host)
│   └── example
├── it   (ccTLD)
│   ├── unibs
│   │   └── ing  (sottodominio)
│   │       └── www
└── arpa  (TLD infrastrutturale — reverse DNS)
    └── in-addr
        └── 192.168.1 (zona inversa per 192.168.1.x)
```

### Namespace — vincoli

| Parametro | Limite |
|-----------|--------|
| Livelli massimi | 127 |
| Lunghezza label (singolo segmento) | 63 caratteri |
| Lunghezza FQDN totale | 253 caratteri |
| Case sensitivity | Case-insensitive |
| Caratteri | Alfanumerici + trattino; Unicode via IDN (punycode) dal 1998 |

---

## Terminologia

| Termine | Definizione |
|---------|------------|
| **FQDN** (Fully Qualified Domain Name) | Nome completo di una macchina, es. `www.ing.unibs.it.` (il punto finale indica la root) |
| **Zona** | Porzione di namespace delegata a un name server specifico |
| **Dominio** | Porzione di namespace che può coprire più zone |
| **TLD** | Top Level Domain — primo livello sotto la root |
| **ccTLD** | Country Code TLD — assegnato per nazione (ISO-3166) |
| **SLD / 2LD** | Second Level Domain — primo livello sotto il TLD |
| **Authoritative NS** | Server DNS che ha autorità su una zona (listato nel NS record della zona padre) |
| **Registro (registry)** | Organismo (delegato da ICANN) che gestisce i NS primari e le assegnazioni di un TLD |
| **Registrante** | Persona/organizzazione che registra un nome di dominio |
| **ICANN** | Internet Corporation for Assigned Names and Numbers — organismo apicale |

### TLD principali

| Tipo | Esempi |
|------|--------|
| **gTLD** (generico) | `.com`, `.net`, `.org`, `.edu`, `.gov`, `.info`, `.biz` |
| **ccTLD** (paese) | `.it`, `.de`, `.uk`, `.fr`; alcuni usati per marketing (`.tv` Tuvalu, `.io` British Indian Ocean) |
| **Infrastrutturale** | `.arpa` (reverse DNS, root server) |
| **Personalizzati** | `.google`, `.apple`, `.bank` — introdotti da ICANN dal 2012 |

---

## Tipi di DNS Server

### NS senza zone (resolver/client)

| Tipo | Funzione |
|------|---------|
| **Ricorsivo** | Esegue tutte le query necessarie per conto del client, percorrendo la gerarchia DNS |
| **Non ricorsivo** | Risponde solo con informazioni che ha già in cache |
| **Caching-only** | Risolve ricorsivamente e memorizza le risposte; non ha zone proprie |
| **Forwarder** | Delega le query a un altro server DNS specificato nella configurazione |

**Resolver pubblici noti**: Google `8.8.8.8 / 8.8.4.4`, Cloudflare `1.1.1.1 / 1.0.0.1`.

### NS con zone (server autoritativo)

| Tipo | Funzione |
|------|---------|
| **Primary / Master** | Detiene il file di zona originale; le modifiche vanno fatte qui |
| **Secondary / Slave** | Copia la zona dal primary via zone transfer; agisce da backup autoritativo |

Un server può essere primary per alcune zone e secondary per altre.

---

## DNS Resource Record (RR)

Formato generale: `[NAME] [TTL] [CLASS] TYPE DATA`

### Record standard

| Tipo | Funzione | Esempio |
|------|---------|---------|
| **SOA** | Start of Authority — inizio zona, parametri globali | `@ IN SOA ns1.esempio.it. admin.esempio.it. (serial refresh retry expire minimum)` |
| **NS** | Name server per il dominio/sottodominio | `@ IN NS ns1.esempio.it.` |
| **A** | Nome → IPv4 | `www IN A 192.168.1.10` |
| **AAAA** | Nome → IPv6 | `www IN AAAA 2001:db8::1` |
| **PTR** | IP → Nome (reverse lookup) | `10 IN PTR www.esempio.it.` (nella zona inversa) |
| **MX** | Mail exchanger — dove inviare la posta | `@ IN MX 10 mail.esempio.it.` |
| **CNAME** | Alias di un host | `ftp IN CNAME www.esempio.it.` |
| **TXT** | Testo libero — usato da SPF, DKIM, verifica dominio | `@ IN TXT "v=spf1 mx -all"` |
| **SRV** | Service record — porta e host per un servizio | `_sip._tcp IN SRV 10 60 5060 sip.esempio.it.` |
| **CAA** | CA autorizzate a emettere certificati per il dominio | `@ IN CAA 0 issue "letsencrypt.org"` |

### Parametri SOA — significato

| Parametro | Significato |
|-----------|------------|
| Serial | Numero di versione del file di zona (formato `YYYYMMDD##`); gli slave controllano se è aumentato per fare zone transfer |
| Refresh | Quanto spesso lo slave interroga il master (es. `8H` = ogni 8 ore) |
| Retry | Intervallo di ritentativi se il master non risponde (es. `2H`) |
| Expire | Per quanto lo slave serve la zona anche senza poter contattare il master (es. `1W`) |
| Minimum/Negative TTL | Quanto i resolver cachano risposte negative (NXDOMAIN) |

---

## Configurazione BIND (Berkeley Internet Name Domain)

### Avvio

```bash
systemctl start named   # oppure
/usr/sbin/named -f /etc/named.conf
```

### File principali

| File | Contenuto |
|------|-----------|
| `/etc/named.conf` | Configurazione principale (opzioni, zone) |
| `/var/named/` | Directory delle zone |
| `/var/named/root.hints` | Indirizzi dei root server |

### named.conf — struttura base

```
options {
    directory "/var/named";
    // forward first;
    // forwarders { 8.8.8.8; };
};

// Zona root hints (per resolver ricorsivo)
zone "." {
    type hint;
    file "root.hints";
};

// Zona locale
zone "esempio.it" {
    type master;
    file "esempio.it";
    // notify yes;
};

// Zona inversa
zone "1.168.192.in-addr.arpa" {
    type master;
    file "192.168.1";
};
```

### Esempio file di zona

```
$TTL 86400
@ IN SOA ns1.esempio.it. admin.esempio.it. (
    2025052801  ; Serial
    8H          ; Refresh
    2H          ; Retry
    1W          ; Expire
    1D )        ; Negative TTL

    IN NS  ns1.esempio.it.
    IN NS  ns2.esempio.it.
    IN MX  10 mail.esempio.it.

ns1   IN A  192.168.1.2
ns2   IN A  192.168.1.3
mail  IN A  192.168.1.4
www   IN A  192.168.1.10
ftp   IN CNAME www.esempio.it.
```

### Configurazione secondary (slave)

```
zone "esempio.it" {
    type slave;
    file "esempio.it";
    masters { 192.168.1.2; };
};
```

---

## ACL e sicurezza in BIND

```
// Definire ACL per limitare chi può fare query/transfer
acl "reti-interne" { 192.168.0.0/16; 10.0.0.0/8; };

options {
    allow-query { "reti-interne"; };
    allow-transfer { 192.168.1.3; };  // solo il secondary
    recursion yes;
    allow-recursion { "reti-interne"; };
};
```

### Best practice sicurezza

- Eseguire `named` in modalità **chroot** (`named -t /var/named/chroot`) per limitare l'accesso al filesystem.
- Separare **autoritative server** (pubblici) da **resolver ricorsivi** (interni) su macchine diverse.
- Limitare zone transfer solo ai secondari autorizzati.
- Abilitare **DNSSEC** per le zone pubbliche.

---

## Diagnostica

| Tool | Uso |
|------|-----|
| `nslookup host [server]` | Query interattiva o one-shot verso un server specifico |
| `dig @server zona tipo` | Query dettagliata con info su autorità e tempi |
| `host nome` | Risoluzione semplice |
| `dig @root-server.net . ns` | Interrogare i root server |
| `whois dominio` | Info sul registrante e NS di un dominio |
| `ipconfig /flushdns` | Svuotare la cache DNS del client Windows |

---

## Alta disponibilità DNS

- Il DNS ha ridondanza **nativa nel protocollo**: ogni zona deve avere almeno NS primario + secondario.
- Ogni client dovrebbe avere ≥2 resolver configurati.
- Quando il primary è irraggiungibile, i secondary continuano a rispondere (fino all'expire SOA).
- **Round-robin DNS**: stesso nome con più record A diversi — distribuisce il carico tra più server.

---

## Evoluzione del DNS

### DDNS (Dynamic DNS)
Aggiornamento automatico dei record A/AAAA quando un host DHCP cambia IP (integrazione DHCP ↔ DNS). In Windows: AD integrazioni native; in Linux: `nsupdate` + TSIG.

### DNSSEC
Firma crittografica dei record DNS per prevenire cache poisoning e spoofing. Aggiunge record RRSIG, DNSKEY, DS, NSEC. Complesso da gestire; essenziale per TLD e domini pubblici critici.

### DoH (DNS over HTTPS)
RFC 8484 — query DNS incapsulate in HTTPS (porta 443). Cifra il traffico DNS, nasconde le query ai provider intermedi. Supportato da Firefox ≥62, Chrome ≥70, Windows 11+.

### DoT (DNS over TLS)
Query DNS cifrate su TLS (porta 853). Simile a DoH ma più semplice da filtrare a livello di rete.

### CoreDNS
Server DNS moderno usato in Kubernetes; plugin-based; supporta **service discovery** dei pod.

---

## DNS nei sistemi operativi

| OS | Resolver | Cache | Svuotare cache |
|----|---------|-------|---------------|
| Windows | DNS Client (servizio) | Sì | `ipconfig /flushdns` |
| Linux (systemd) | `systemd-resolved` | Sì | `resolvectl flush-caches` |
| Linux (classic) | `/etc/resolv.conf` + Unbound | Dipende | `service unbound restart` |
| macOS | `mDNSResponder` | Sì | `sudo dscacheutil -flushcache` |

---

## Articoli correlati

- [[ntp-e-dhcp]]
- [[servizi-infrastrutturali]]
- [[linux-log-monitoraggio-rete]]
- [[server-windows]]
- [[mail-server]]

## Fonti

- `raw/3 - Servizi_COMPILED.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
