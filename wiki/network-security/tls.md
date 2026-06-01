---
tags: [network-security, TLS, SSL, handshake, IPSec, session-key, PFS, cipher-suite]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-2b1-tls.pdf
---

# TLS — Transport Layer Security

**TLS** (Transport Layer Security) è il protocollo di sicurezza più diffuso su Internet. Si colloca tra il livello di trasporto (TCP/UDP) e le applicazioni, fornendo autenticazione, integrità e confidenzialità in modo indipendente dall'applicazione. La versione per UDP si chiama **DTLS**.

## Punti chiave

- TLS opera **sopra TCP/UDP**, IPSec opera **sotto** (a livello IP) — approcci complementari con trade-off diversi.
- TLS può essere implementato nell'applicazione, senza modifiche all'OS; IPSec richiede modifiche al kernel.
- Il TLS Handshake è **4-way** e deriva il Master Secret K_ms (384 bit), da cui si derivano tutte le chiavi di sessione.
- **PMS** (Pre-Master Secret): scelto casualmente dal client, cifrato con K_pubB; K_ms = f_PMS(R_A, R_B).
- **TLS 1.2**: PFS opzionale (K_ms da PMS=DH); **TLS 1.3**: PFS sempre obbligatorio.
- Da K_ms si derivano 6 chiavi: 3 per il client (MAC, cifratura, IV) e 3 per il server.
- Autenticazione server: sempre; autenticazione client: opzionale.

## Posizione nello stack (TLS vs IPSec)

```
APP
 │
TLS ← tra transport e app
 │
TCP/UDP
 │
IPSec ← tra IP e transport (OS level)
 │
IP
```

| Protocollo | Layer | Pros | Cons |
|---|---|---|---|
| **TLS** | Application | No OS changes; può usare qualsiasi credenziale applicativa | DoS su TCP abbatte anche TLS; piccole modifiche alle app |
| **IPSec** | Network | Parzialmente in HW; nessuna modifica agli strati superiori; cifra tutto il traffico verso una destinazione | Modifica kernel OS; cifra tutto il traffico (granularità bassa) |

## Storia di TLS

| Anno | Versione | Note |
|---|---|---|
| 1995 | SSL v2 (Netscape) | Protezione sessioni HTTP; proibito RFC 6176 (2011) |
| 1997 | SSL v3 | Bug fixes; usato >15 anni; deprecated RFC 7568 (2015) "not sufficiently secure" |
| 1999 | TLS 1.0 (IETF) | SSLv3 standardizzato; aggiunge DSS (workaround brevetto RSA) |
| 2008 | TLS 1.2 (RFC 5246) | Standard di riferimento per il corso |
| 2012 | DTLS 1.2 (RFC 6347) | TLS sopra UDP |
| 2018 | TLS 1.3 (RFC 8446) | PFS obbligatorio; handshake più veloce |
| 2022 | DTLS 1.3 (RFC 9147) | |

Il corso si focalizza su **TLS 1.2** con keypair DSS/RSA in certificati X.509.

## Architettura TLS 1.2

```
Applications
     │
┌────┴────────────────────┐
│  TLS Handshake (H.)     │
│  TLS ChangeCipherSpec   │  ← sub-protocolli di gestione
│  TLS Alert (A.)         │
│─────────────────────────│
│    TLS Record Protocol  │ ← trasporto per tutti i messaggi TLS
└────┬────────────────────┘
     │
    TCP
```

**Sub-protocolli:**
- **Handshake (H.)**: negozia cipher suite, autentica, deriva K_ms
- **ChangeCipherSpec (C.)**: messaggio da 1 bit che segnala la fine della parte in chiaro dell'handshake
- **Alert (A.)**: segnala condizioni di errore (es. problemi TCP sottostante)
- **Record Protocol**: trasporta e tipizza i messaggi degli altri sub-protocolli; gestisce le connessioni dati

**Sessione vs connessione**: una **TLS session** (K_ms + sess-id) può ospitare più **TLS connections** (multiplexabili su una singola TCP connection). K_ms = 384 bit (48 byte).

## TLS 1.2 Handshake (4-way)

**Goal**: autenticazione di Bob verso Alice (+ opzionalmente Alice verso Bob) e setup di K_ms.

```
Alice (client)                         Bob (server)
  ① Cipher suite list, R_A    ─────→
                               ←─────  ② Cipher suite scelta, CERT_B, R_B, sess-id
  ③ [CERT_A], [Auth_A],        ─────→
     E_K_pubB(PMS),
     E_Kms(hash_A)
                               ←─────  ④ E_Kms(hash_B)
  ─── Dati utente cifrati (TLS Record Protocol) ───
```

**Terminologia:**
| Simbolo | Significato |
|---|---|
| R_A, R_B | Nonce casuali (random numbers) |
| PMS | Pre-Master Secret: numero casuale scelto da Alice |
| K_ms | Master Secret = f_PMS(R_A, R_B), con f = MAC (es. SHA-256) |
| Auth_A | Firma di Alice: SIG_K_privA(hash(messaggi precedenti)) |
| hash_A/B | g_Kms({client/server}, messaggi precedenti), HMAC da SHA-256+ |

**Nota**: il messaggio (2) di solito contiene più messaggi TLS in un unico record: `server_hello`, `certificate`, `[certificate_request]`, `server_hello_done`.

### Autenticazione

- **Bob autenticato**: Alice verifica CERT_B (e quindi K_pubB); poi il fatto che Bob riesce a decifrare PMS (cioè calcola K_ms corretto) prova che possiede K_privB
- **Alice autenticata (opzionale)**: Bob verifica CERT_A e la firma Auth_A

### Derivazione delle chiavi di connessione

Da K_ms + key expansion (basata su MAC di K_ms, R_A, R_B) si derivano **6 chiavi** (3 per direzione):

| Direzione | MAC key | Cifratura key | IV |
|---|---|---|---|
| Client write | K_cm | K_c | IV_c |
| Server write | K_sm | K_s | IV_s |

Le chiavi sono diverse nelle due direzioni → resistenza ai reflection attack.

### PFS in TLS

- **TLS 1.2**: PFS **opzionale** — K_ms può essere derivato con PMS=DH key invece di PMS RSA
- **TLS 1.3**: PFS **obbligatorio** — sempre basato su DH

### CA hierarchies

- Durante l'handshake, A/B possono inviare **certificate chains** (non solo il proprio certificato)
- Quando B richiede che A si autentichi, B include la lista delle CA ammissibili (known and admissible CAs)

## Session Resumption (TLS 1.2)

Una volta stabilita una TLS session (K_ms + sess-id), A e B possono aprire nuove connessioni **senza un nuovo handshake completo**:
```
Alice → Bob: sess-id, Cipher suite list, R_A
Bob → Alice: sess-id, Cipher suite scelta, R_B, E_Kms(hash_B)
Alice → Bob: E_Kms(hash_A)
```
R_A, R_B e la cipher suite sono re-negoziate; K_ms rimane lo stesso. **In TLS 1.3 la renegotiation è vietata** e la session resumption funziona diversamente.

## TLS 1.3

**Differenze principali da TLS 1.2:**

| Aspetto | TLS 1.2 | TLS 1.3 |
|---|---|---|
| Handshake | 4-way (2 RTT) | 2-way (1 RTT) |
| PFS | Opzionale | **Sempre** (ECDHE obbligatorio) |
| Key exchange | RSA o DH | Solo **ECDHE** (RSA rimosso) |
| Renegotiation | Possibile | Vietata |
| Encryption | Handshake in chiaro + dati cifrati | Dati già cifrati dopo il primo RTT |
| Modello cifratura | Separato encrypt + MAC | **AEAD** (Authenticated Encryption with Associated Data, RFC 5116) |
| Compressione | Opzionale | Rimossa |

**TLS 1.3 Handshake (2-way):**
```
Alice → Bob: ① ClientHello + Capabilities (key_share, signature_algorithms, ...)
Bob → Alice: ② ServerHello + key_share + {EncryptedExtensions} + {Certificate*} + {CertificateVerify*} + {Finished} [+ ApplicationData*]
Alice → Bob: ③ {Certificate*} + {CertificateVerify*} + {Finished} [+ ApplicationData]
```
`{}` = protetto con handshake_traffic_secret; `[]` = protetto con application_traffic_secret.

**ChangeCipherSpec in TLS 1.3**: non segnala più una transizione reale — inviato solo per compatibilità con middlebox (NAT, firewall) che bloccherebbero sessioni senza di esso.

**Adozione (ssllabs.com)**: TLS 1.3 è ormai al ~75% (aprile 2026), TLS 1.2 ~100% (quasi tutti i server supportano entrambi). Best practice: abilitare TLS 1.2 come minimo e TLS 1.3 se supportato.

**TLS 1.4** (ipotetico, IETF draft, aprile 2026 ancora non standardizzato): obiettivo Post-Quantum Cryptography (PQC) + performance IoT/mobile.

## Record Protocol (dettaglio)

```
Dati applicazione
     ↓ Frammentazione
  [Data] [Data] [Data]
     ↓ [Compressione — assente in TLS 1.3]
     ↓ Integrity protection con K_cm → HMAC
  [Data + MAC]
     ↓ Cifratura con K_c (+ IV_c)
  [Ciphertext]
     ↓ TLS Header (tipo, versione, lunghezza)
  [TLS | Ciphertext] → trasmesso
```

**Nota IV**: il primo TLS record di una connessione usa l'IV derivato da K_ms; i successivi seguono la specifica RFC.
MAC = HMAC in TLS; HMAC-like in SSLv3.

## Cipher suites

Una **cipher suite** definisce l'intero insieme di algoritmi, modi operativi e parametri crittografici.

**Formato nome** (TLS 1.2): `KeyExchange_Auth_WITH_Cipher_Mode_MAC`

**Esempi TLS 1.2**: `3DES-CBC+HMAC-MD5+SHA1`, `AES128-CBC+HMAC-SHA256`

**Esempi TLS 1.3** (solo AEAD, no key exchange separato):
- `AES-128-GCM-SHA256`
- `AES-256-GCM-SHA384`
- `CHACHA20-POLY1305-SHA256`

**Negoziazione**: client invia lista ordinata per preferenza; server sceglie quella più alta che supporta.

**ATTENZIONE**: **mai usare cipher suites "EXPORTABLE"** — usano chiavi effimere da soli 40 bit (legacy: controlli export USA anni '90). Assolutamente insicure.

## Attacchi noti a TLS/SSL

### Heartbleed (OpenSSL 1.0.1, scoperto 3/1/2014)
- **Tipo**: memory disclosure tramite bug implementativo
- **Causa**: estensione Heartbeat (keepalive) introdotta in OpenSSL 1.0.1 (3/2012) senza adeguata verifica; includeva un'estesa `memcpy` senza controllare che la lunghezza dichiarata nel messaggio TLV corrispondesse alla lunghezza reale del payload
- **Exploit**: Trudy invia `HB_req[len=10000, "dead"]` (4 byte reali); il server risponde copiando 10000 byte a partire da "dead" in memoria → espone K_privB, password, sessioni attive
- **Impatto**: ~500k server; long-term key compromessa → avrebbero dovuto revocare e rinnovare tutti i certificati; molti non l'hanno fatto → **peggiore vulnerabilità di tutti i tempi**
- **Fix**: OpenSSL 1.0.1g; **disabilitare Heartbeat**

### POODLE (10/2014, CVE-2014-3566)
- **Tipo**: Padding Oracle On Downgraded Legacy Encryption
- **Causa**: SSLv3 usa CBC con padding vulnerabile (L-1 byte random + byte L); nessun integrità sul padding
- **Exploit**: Trudy intercetta il traffico e finge errori server ripetutamente → client fa downgrade da TLS 1.2 a SSLv3; poi Trudy sostituisce C₃ con C₁ e osserva se la sessione è chiusa → se no, ha decifrato un byte; ripetendo su centinaia di sessioni recupera cookie/credenziali
- **Fix**: **disabilitare SSLv3**

### Altri attacchi

| Attacco | Anno | CVE | Causa | Fix |
|---|---|---|---|---|
| **BEAST** | 2011 | CVE-2011-3389 | IV predicibile in CBC con SSL3/TLS1.0; MITM può determinare IV | Upgrade a TLS 1.1+ |
| **CRIME** | 2012 | CVE-2012-4929 | Info leak tramite ratio di compressione TLS | Disabilitare compressione TLS |
| **BREACH** | 2013 | CVE-2013-3587 | Info leak tramite compressione HTTP | Disabilitare compressione HTTP |
| **Drown** | 2016 | CVE-2016-0800 | Server SSLv2 usato come oracolo per decifrare sessioni TLS passate; padding RSA | Disabilitare SSLv2 su tutti i server |

**Drown**: day 0, il 33% di tutti i server TLS era vulnerabile (avevano ancora SSLv2 abilitato). Oggi <1%.

## Note finali sull'uso di TLS

- TLS oggi è usato principalmente per HTTPS (one-way auth: server si autentica con certificato; client si autentica a livello applicativo con password)
- Altri usi: tunnel sicuro su TCP/UDP (es. `stunnel`), protezione di protocolli di autenticazione (es. **EAP-TLS** — vedi sicurezza wireless)
- DoS su TCP abbatte la sessione TLS → considerare sempre questa superficie di attacco

## Articoli correlati

- [[pki]]
- [[protocolli-crittografici]]
- [[crittografia-simmetrica]]
- [[funzioni-hash]]
- [[diffie-hellman]]

## Fonti

- raw/2026-netsec-2b1-tls.pdf
