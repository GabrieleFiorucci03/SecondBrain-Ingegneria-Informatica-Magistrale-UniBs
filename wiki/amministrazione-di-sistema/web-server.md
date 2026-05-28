---
tags: [web, http, apache, nginx, ssl, tls, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/3 - Servizi_COMPILED.pdf
---

# Web Server

Il web server è un processo server (demone) che risponde alle richieste HTTP/HTTPS dei client, servendo pagine HTML, risorse statiche e contenuti dinamici. I protocolli HTTP/HTTPS sono ormai usati ben oltre il web tradizionale: API REST, WebDAV, Object Storage (S3), DoH, SSL VPN.

## Punti chiave

- **HTTP è il protocollo applicativo** del WWW; le risorse sono in formato MIME (HTML, testo, immagini, ecc.).
- **Versioni HTTP**: 1.0 (connessione per richiesta) → 1.1 (persistent connections, pipelining) → 2.0 (multiplexing, header compression, binary) → 3.0 (HTTP su QUIC/UDP).
- **Apache HTTPd** e **nginx** dominano il mercato; nginx ha superato Apache nel 2022 (≈20% vs 18%).
- **Virtual hosting**: più siti sullo stesso server (per IP, per nome, per porta).
- **HTTPS**: HTTP + TLS/SSL tramite `mod_ssl` (Apache) o configurazione nativa (nginx); richiede un certificato valido (CA pubblica o privata).
- **Scalabilità**: scale-out tramite cluster di load balancing; CDN per distribuzione geografica dei contenuti statici.
- **Sicurezza web**: HTTPS, WAF, protezione anti-DDoS, isolamento siti (PHP safe mode), autenticazione HTTP.

---

## Protocollo HTTP

### Versioni

| Versione | Anno | Innovazioni chiave | Trasporto |
|----------|------|--------------------|-----------|
| HTTP/1.0 | 1996 | Una connessione per richiesta | TCP |
| HTTP/1.1 | 1999 | Persistent connections, pipelining, cache, Host header | TCP |
| HTTP/2.0 | 2015 | Multiplexing, header compression (HPACK), binary, server push | TCP+TLS |
| HTTP/3 | 2022 (RFC 9114) | HTTP su QUIC (UDP), 0-RTT resumed connections, no HoL blocking | UDP (QUIC) |

### Perché HTTP/2?

HTTP/1.1 soffre di **Head-of-Line Blocking**: con il pipelining, se una risposta è lenta blocca tutte le successive sullo stesso canale. HTTP/2 risolve con:
- Tutte le richieste in parallelo su una singola connessione TCP (multiplexing via stream).
- Header compressi (riduzione overhead).
- Trasferimento binario (più efficiente del testo).
- Server push (il server invia risorse prima che il client le richieda).

### Perché HTTP/3 (QUIC)?

TCP ha head-of-line blocking a livello di trasporto: un pacchetto perso blocca tutti gli stream. QUIC (basato su UDP) implementa:
- Stream indipendenti → la perdita di un pacchetto impatta solo lo stream corrispondente.
- Handshake combinato QUIC+TLS in 1RTT (o 0RTT per connessioni riprese).
- Migrazione di connessione (es. cambio da WiFi a 4G senza perdere la sessione).

### Header HTTP principali (v1.x)

Formato: `[Richiesta|Risposta] Header: Valore`

Comuni: `Host`, `Accept`, `Accept-Encoding`, `Authorization`, `Content-Type`, `Cache-Control`, `Cookie`, `Set-Cookie`, `Location` (redirect), `Server`.

Codici di risposta: `200 OK`, `301 Moved Permanently`, `304 Not Modified`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `500 Internal Server Error`.

---

## Apache HTTPd

Il server web open source più diffuso storicamente. Attualmente alla versione 2.4.x.

### Architettura — Multi-Processing Module (MPM)

| MPM | Modello | Pro | Contro |
|-----|---------|-----|--------|
| **Pre-fork** | Un processo per richiesta | Massima stabilità (crash isolato) | Consumo memoria elevato |
| **Worker** | Processi multi-thread | Buone prestazioni, minore RAM | Crash thread impatta connessioni attive |
| **Event** (default 2.4) | Thread + event loop | Gestione efficiente keep-alive | Più complesso |

### File di configurazione

| File | Contenuto |
|------|-----------|
| `/etc/httpd/conf/httpd.conf` | Configurazione principale (include altri file) |
| `/etc/httpd/conf.d/` | File di configurazione modulari |

### Direttive essenziali

```apache
ServerRoot    /etc/httpd
DocumentRoot  /var/www/html
ServerName    www.esempio.it
ServerAdmin   webmaster@esempio.it
User          apache
Group         apache

# Processi
StartServers       10
MinSpareServers    8
MaxSpareServers    20
MaxClients         150
MaxRequestsPerChild 500

# Log
ErrorLog   logs/error_log
LogLevel   warn
CustomLog  logs/access_log combined

# Gestione errori custom
ErrorDocument 404 /errori/404.html
```

### Moduli principali

```apache
LoadModule ssl_module        modules/mod_ssl.so
LoadModule rewrite_module    modules/mod_rewrite.so
LoadModule php_module        modules/libphp8.so
LoadModule status_module     modules/mod_status.so
```

I moduli sono **dinamici** (`.so`): si aggiungono senza ricompilare Apache; basta `LoadModule` + riavvio.

### Virtual hosting

**Per IP**: ogni sito ha un IP dedicato; compatibile con tutti i browser.
```apache
<VirtualHost 192.168.1.10>
    DocumentRoot /var/www/sito1
    ServerName   sito1.esempio.it
</VirtualHost>
```

**Per nome** (SNI): IP condiviso, discriminazione tramite header `Host`; il metodo standard oggi.
```apache
NameVirtualHost *:80
<VirtualHost *:80>
    DocumentRoot /var/www/sito2
    ServerName   sito2.esempio.it
</VirtualHost>
```

**Per porta**: utile in contesti specifici; HTTPS è essenzialmente un virtual host sulla porta 443.

### Aree protette

Per IP:
```apache
<Location "/admin">
    Require ip 192.168.1.0/24
</Location>
```

Per utente (Basic Auth):
```apache
<Location "/riservato">
    AuthUserFile /etc/httpd/htpasswd
    AuthName     "Area riservata"
    AuthType     Basic
    Require user mario
</Location>
```
Utenti creati con: `htpasswd -c /etc/httpd/htpasswd mario`

---

## Nginx

Alternativa ad Apache con architettura **event-driven asincrona** (nessun processo/thread per richiesta) — ottimo per connessioni concorrenti elevate e contenuti statici.

### Contesti di configurazione

```
Main → Events
     → HTTP → Server → Location
            → Upstream
     → Stream → Server
               → Upstream
```

### Configurazione tipica

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name www.esempio.it;
    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    listen 443 ssl http2;
    ssl_certificate     /etc/ssl/certs/esempio.crt;
    ssl_certificate_key /etc/ssl/private/esempio.key;
}
```

### Uso comune: reverse proxy + Apache

- Nginx riceve le richieste esterne (SSL offloading, contenuti statici).
- Nginx inolta le richieste dinamiche ad Apache o Tomcat (localhost).
- Benefici: prestazioni, SSL centralizzato, cache statica.

---

## HTTPS e certificati SSL/TLS

### Flusso

1. **Generare CSR** (Certificate Signing Request) con chiave privata.
2. **Inviare CSR** a una CA (Certification Authority).
3. **Ricevere certificato** firmato dalla CA.
4. **Installare** certificato + chiave sul server web.
5. **Rinnovare** prima della scadenza.

### Tipi di certificato

| Tipo | CA | Uso |
|------|----|-----|
| **Self-signed** | Nessuna CA pubblica | Test, reti interne; genera warning nel browser |
| **DV** (Domain Validated) | CA pubblica | Verifica solo la proprietà del dominio; gratuito con Let's Encrypt |
| **OV** (Organization Validated) | CA pubblica | Verifica l'organizzazione |
| **EV** (Extended Validation) | CA pubblica | Verifica approfondita; più trust visivo |

**Let's Encrypt**: CA gratuita e automatizzata (ACME protocol via `certbot`); rinnovo ogni 90 giorni.

### Configurazione Apache SSL

```apache
<VirtualHost *:443>
    ServerName www.esempio.it
    DocumentRoot /var/www/html

    SSLEngine on
    SSLCertificateFile    /etc/ssl/certs/esempio.crt
    SSLCertificateKeyFile /etc/ssl/private/esempio.key
    SSLCACertificateFile  /etc/ssl/certs/ca-chain.crt

    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5
</VirtualHost>
```

### Tipi di autenticazione HTTP

| Tipo | Meccanismo | Sicurezza |
|------|-----------|----------|
| **Basic** (RFC 7617) | Base64(user:pass) nell'header | Sicuro solo su HTTPS |
| **Digest** | Hash della password | Obsoleto |
| **Kerberos/AD** | SSO integrato con Active Directory | Enterprise |
| **Bearer** (OAuth 2.0, RFC 6750) | Token JWT nell'header | Moderno, standard API |
| **Client certificates** (mTLS) | Certificato lato client | Massima sicurezza |

---

## Pagine dinamiche e stack

### Tecnologie lato server

| Tecnologia | Meccanismo | Stack tipico |
|-----------|-----------|--------------|
| **CGI** | Script separato eseguito per ogni richiesta | Perl, Python, Shell |
| **SSI** (Server Side Include) | Direttive HTML processate dal server | Apache |
| **PHP** (mod_php o php-fpm) | Interprete integrato/FastCGI | LAMP (Linux+Apache+MySQL+PHP) |
| **ASP.NET** | Runtime .NET | WAMP o IIS+Windows |
| **Node.js** | JavaScript asincrono | Nginx come reverse proxy |
| **Java (Tomcat)** | Servlet/JSP | Apache+Tomcat o nginx+Tomcat |

### Stack comuni

| Stack | Componenti |
|-------|-----------|
| **LAMP** | Linux + Apache + MySQL/MariaDB + PHP |
| **LEMP** | Linux + Nginx + MySQL + PHP |
| **WAMP** | Windows + Apache + MySQL + PHP |
| **J2EE** | Tomcat/JBoss + Java + DB |

---

## Prestazioni e scalabilità

### Bottleneck per tipo di contenuto

| Contenuto | Bottleneck principale |
|-----------|----------------------|
| Statico (HTML, immagini) | Rete, I/O disco |
| Dinamico (PHP, Python) | CPU, RAM |
| API/DB-backed | Latenza database, CPU |

### Tecniche di ottimizzazione

| Tecnica | Strumento | Beneficio |
|---------|-----------|----------|
| **Cache contenuti statici** | `mod_file_cache`, Varnish | Elimina I/O disco |
| **Cache applicativa** | Redis, Memcached | Elimina query DB ripetute |
| **CDN** | Cloudflare, Akamai, AWS CloudFront | Distribuzione geografica, riduzione latenza |
| **Reverse proxy cache** | Nginx, Varnish | Riduce carico sul backend |
| **Compressione** | `mod_deflate` (Apache), `gzip` (nginx) | Riduce dimensione risposta |
| **SSL offloading** | HAProxy, nginx | Sposta il carico TLS dall'app server |
| **Load balancing** | HAProxy, nginx upstream | Distribuisce richieste tra più backend |

### Benchmark

- **ab** (Apache Benchmark): `ab -n 1000 -c 100 http://server/`
- Servizi online: WebPageTest, GTmetrix, Lighthouse.

### Architettura scalabile tipica

```
Internet → [CDN] → [Load Balancer] → [Web Server 1..N]
                                    → [App Server 1..N]
                                    → [DB Cluster]
```

---

## Sicurezza web

| Minaccia | Mitigazione |
|----------|------------|
| **DDoS** | Rate limiting (`mod_evasive`), CDN con protezione DDoS, WAF |
| **XSS** (Cross-Site Scripting) | Content Security Policy (CSP), sanitizzazione input |
| **SQL Injection** | Prepared statements, WAF |
| **Sniffing** | HTTPS obbligatorio (HSTS) |
| **Accesso non autorizzato** | Autenticazione, ACL per IP, fail2ban |
| **Contenuti dinamici insicuri** | Isolamento PHP (`php-fpm` + user separati), `open_basedir` |

**WAF** (Web Application Firewall): analizza il traffico HTTP a livello applicativo (es. `mod_security` per Apache, Nginx + ModSecurity).

---

## Articoli correlati

- [[mail-server]]
- [[ftp-server]]
- [[dns-server]]
- [[servizi-infrastrutturali]]
- [[linux-log-monitoraggio-rete]]
- [[server-windows]]

## Fonti

- `raw/3 - Servizi_COMPILED.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
