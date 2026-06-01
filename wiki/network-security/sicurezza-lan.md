---
tags: [network-security, LAN, 802.1x, EAP, EAPOL, RADIUS, AAA, autenticazione-rete]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-2c1-lan.pdf
---

# Sicurezza nelle LAN (IEEE 802.1x, EAP, RADIUS)

La sicurezza nelle LAN a livello data-link mira a garantire che solo utenti **autenticati e autorizzati** possano accedere alla rete. L'architettura standard si basa su **IEEE 802.1x** (port access control), **EAP** (protocollo di autenticazione flessibile) e **RADIUS** (server AAA).

## Punti chiave

- IEEE 802.1x blocca ogni porta di rete fino al completamento dell'autenticazione.
- L'**Authenticator** (switch/AP) è un puro **relay** — non partecipa attivamente al protocollo di autenticazione.
- **EAP** non è un protocollo di autenticazione, ma un **trasportatore** di protocolli di autenticazione (EAP methods).
- **RADIUS** è il protocollo AAA più usato; i meccanismi crittografici interni sono deboli — **eseguire sempre sopra IPSec o TLS**.
- EAP-MD5 è il metodo minimo obbligatorio ma è **debole e non-mutuale** — usare EAP-TLS o EAP-tunnel-based.
- I metodi TLS-tunnel (EAP-PEAP, EAP-TTLS) avvolgono PAP/CHAP in un tunnel TLS → credenziali semplici con sicurezza forte.

## Architettura generale

```
Supplicant ←─EAPOL─→ Authenticator ←─RADIUS/AAA─→ Authentication Server
(client)               (switch/AP)                   (AAA server)
```

**Tre entità IEEE 802.1x:**

| Entità | Ruolo |
|---|---|
| **Supplicant** | Client/terminale che vuole accedere alla rete |
| **Authenticator** | Porta di un dispositivo 802.1x (switch, AP, router); è un **relay** puro: non partecipa al protocollo di autenticazione |
| **Authentication Server (AS)** | Esegue il protocollo di autenticazione con il Supplicant; dice all'Authenticator cosa fare (apri porta, chiudi porta, ...) |

**Porta** (concetto di 802.1x): può essere fisica (es. porta Ethernet di uno switch 802.3) o virtuale (es. sessione tra AP e client in 802.11).

## IEEE 802.1x — Port-based Network Access Control

Il meccanismo di controllo degli accessi:

```
Supplicant → [Auth Protocol] → Authenticator
                                   │ PAE (Port Access Entity)
                                   ├─ Port Authorize (default: chiusa)
                                   ├─ MAC enable
                                   └─ → Authentication Server
```

1. Prima dell'autenticazione: la porta è chiusa, passa solo il traffico di autenticazione
2. L'AS esegue il protocollo di autenticazione con il Supplicant (attraverso il relay)
3. Se successo: l'AS dice all'Authenticator di aprire la porta
4. Il traffico "normale" può ora fluire

## EAP — Extensible Authentication Protocol

**EAP** (RFC 3748) non è un protocollo di autenticazione — è un **framework di trasporto** che permette di negoziare e usare diversi metodi di autenticazione (**EAP methods**):

- EAP-MD5 (obbligatorio, debole)
- EAP-AKA (reti mobili)
- EAP-SIM (reti mobili)
- **EAP-TLS** (forte, certificati su entrambi i lati)
- EAP-PEAP, EAP-TTLS (TLS tunnel con PAP/CHAP dentro)

**Trasporto EAP:**
- Tra Supplicant e Authenticator: **EAPOL** (EAP over LAN) — layer 2 (PPP, 802.3, ...)
- Tra Authenticator e AS: protocollo di livello applicativo (**AAA**, es. RADIUS)

## EAPOL — EAP over LAN

5 tipi di pacchetto EAPOL:

| Tipo | Significato |
|---|---|
| **EAPOL Start** | Inizio del processo (può essere inviato da Supplicant o Authenticator) |
| **EAP auth packets** | I messaggi del metodo di autenticazione specifico |
| **EAP Success** | Autenticazione riuscita |
| **EAP Failure** | Autenticazione fallita |
| **EAPOL Key** | Trasferimento delle chiavi effimere, se previsto dal metodo |
| **EAPOL Logoff** | Fine della sessione |

**Flusso tipico EAPOL** (esempio con metodo negoziato):
```
Supplicant → EAPOL Start → Authenticator
           ← EAP Request/ID
           → EAP Response/ID (es. "gringoli@unibs.it")
           ← EAP Request/MD5-Challenge RAND     [AS suggerisce EAP-MD5]
           → EAP Response/NAK EAP-AKA           [Supplicant rifiuta, vuole EAP-AKA]
           ← ... AKA Exchange ...
           ← EAP Success
           ← EAPOL Key (chiavi effimere)
           → EAPOL Logoff (fine sessione)
```

L'Authenticator fa da relay e non ha bisogno di sapere quale metodo di autenticazione stanno usando → facilità di aggiornamento.

## AAA — Authentication, Authorization, Accounting

**AAA** è il framework del lato server:
- **Authentication**: verificare l'identità
- **Authorization**: determinare cosa può fare
- **Accounting**: tenere traccia dell'uso (billing, log)

**AAA Server**: mantiene il database utenti, le policy di accesso, i dati di billing.

**AAA Protocol**: protocollo tra il Network Access Server (NAS = Authenticator in 802.1x) e il AAA server. Il protocollo AAA più usato è **RADIUS**.

## RADIUS — Remote Authentication Dial-In User Service

RFC 2865; il protocollo AAA più diffuso. Accounting: RFC 2866.

**Architettura**: client/server
- **RADIUS client** = NAS (switch 802.3, RNC UMTS, PPP server, ...) — coincide con l'Authenticator in 802.1x
- **RADIUS server** = AS

**Caratteristiche:**
- Lavora sopra **UDP** (retransmissioni e packet loss gestiti a livello applicativo)
- Trasporta: dati di autenticazione (PAP, CHAP, EAP, ...), dati di autorizzazione, accounting
- **Extensible**: RADIUS attributes possono trasportare molti tipi di dati
- Requests e responses matchate tramite un **identifier**

### RADIUS + PAP (esempio)
```
Alice → RADIUS client: {A, pwd}
RADIUS client → AS: RADIUS access request {User-Name=A, User-Password=f_s(pwd)}
  [f_s = PRF con PSK tra NAS e RADIUS server]
AS: lookup(A) → {salt, h}; verifica h = f(pwd|salt)
AS → RADIUS client: RADIUS access accept
```
*Nota: l'autorizzazione avviene prima dell'autenticazione in RADIUS.*

### RADIUS + CHAP (esempio)
```
Alice ← RADIUS client: c (challenge)
Alice → RADIUS client: A, r=f(pwd,c)
RADIUS client → AS: RADIUS access request {User-Name=A, CHAP-Password=r, CHAP-Challenge=c}
AS: verifica r'=f(pwd,c)=r?  [pwd in cleartext sul AS!]
AS → RADIUS client: RADIUS access accept
```

### RADIUS + EAP (RFC 3579, 5080)
Qualsiasi EAP method può essere usato con RADIUS. Flusso completo:
```
Supplicant ←─EAPOL─→ Authenticator ←────RADIUS────→ AS
EAPOL Start →
← EAP Request/ID
→ EAP Response/ID                     → RADIUS access request (EAP-Response/ID)
                                       ← RADIUS access challenge (EAP-Request/MD5-Challenge)
← EAP Request/MD5-Challenge
→ EAP Response/MD5-response           → RADIUS access request (EAP-Response/MD5-response)
                                       ← RADIUS access accept
← EAP Success
```

### Note di sicurezza RADIUS

**Problemi:**
- PSK usata per autenticare client/server e cifrare attributi confidenziali (es. password PAP)
- Molti parametri in **cleartext** (user name, challenge, ecc.)
- I meccanismi crittografici di RADIUS sono **deboli**

**Regola**: **eseguire sempre RADIUS sopra IPSec o TLS** (RADSEC).

**Altri aspetti:**
- **Proxy RADIUS**: deployment gerarchico per federated auth
- **DIAMETER**: evoluzione di RADIUS (TCP/SCTP, più robusto, nativo nelle reti 4G/5G)

## Metodi EAP più usati: TLS-tunnel-based

Idea: avvolgere un metodo debole (PAP, CHAP) dentro un tunnel TLS.

**Flusso:**
```
Client ←─TLS over EAP─→ AS     [AS autentica a Client tramite certificato]
       ─── TLS tunnel ───────────
            EAP-PAP              [credenziali utente dentro il tunnel]
       ─── ─── ─── ─── ─────────
Client e AS derivano PMK (Pairwise Master Key)
AS → RADIUS client: EAP-ACCEPT (PMK)
Client → AP: PMK → PTK (EAP/802.11i)
```

**Pro**: le credenziali utente rimangono semplici da gestire (username/password).
**Contro**: overhead di rete e computazionale (TLS + metodo interno).

**Metodi popolari**: EAP-PEAP (Microsoft), EAP-TTLS, EAP-FAST.

## EAP-MD5: il metodo più debole

- Obbligatorio per ogni implementazione EAP
- MD5-response = MD5(Challenge | password)
- **Debole**: non-mutuale (il server non si autentica al client); password vulnerabile a dictionary attack
- **Non usare EAP-MD5** in produzione

## Articoli correlati

- [[protocolli-crittografici]]
- [[tls]]
- [[pki]]
- [[sicurezza-wireless]]

## Fonti

- raw/2026-netsec-2c1-lan.pdf
