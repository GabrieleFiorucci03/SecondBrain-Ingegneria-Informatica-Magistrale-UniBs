---
tags: [ntp, dhcp, rete, servizi, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/3 - Servizi_COMPILED.pdf
---

# NTP e DHCP

NTP e DHCP sono due servizi infrastrutturali fondamentali presenti in qualsiasi rete: NTP garantisce la consistenza temporale dei sistemi; DHCP automatizza la configurazione degli indirizzi IP.

## Punti chiave

- **NTP**: compensa il drift dell'orologio hardware (spesso di scarsa qualità o virtualizzato); l'ora corretta è prerequisito per Kerberos, log correlabili, certificati TLS, transazioni finanziarie.
- **PTP**: alternativa sub-microsecondo a NTP per applicazioni che richiedono precisione estrema (trading, telco, array acustici).
- **DHCP**: evoluzione di RARP e BOOTP; distribuisce IP, gateway, DNS e nome di dominio; basato su UDP (porta 67 server, 68 client); supporta allocazione dinamica e statica (reservation per MAC).
- **APIPA**: fallback automatico Windows (169.254.x.x) quando DHCP non risponde.
- **DHCP Relay**: permette a un unico server DHCP di servire più subnet tramite un agente relay; alternativa ai server multipli per subnet.
- **Split scope 80/20**: regola di ridondanza DHCP — il server primario gestisce l'80% degli indirizzi, il secondario il 20%.

---

## NTP (Network Time Protocol)

### Cos'è

Uno dei protocolli più vecchi ancora in uso (v4, RFC 5905). Stima e compensa gli errori sistematici dell'orologio hardware tramite confronto con fonti di riferimento gerarchiche.

### Struttura gerarchica (stratum)

```
Stratum 0: Orologi atomici (GPS, cesio) — non connessi direttamente alla rete
Stratum 1: Server direttamente connessi a stratum 0
Stratum 2: Server sincronizzati da stratum 1 (si scambiano anche tra loro)
Stratum 3: Server sincronizzati da stratum 2
...
Stratum N (client finali)
```

- Ogni livello aggiunge latenza e potenziale imprecisione.
- I client dovrebbero puntare allo stratum più basso disponibile.
- Servizi pubblici di riferimento: `time.ien.it`, `pool.ntp.org`.

### Implementazioni

| Piattaforma | Software | Note |
|------------|---------|------|
| Linux | `ntpd` (ISC), `chrony`, `systemd-timesyncd` | `chrony` preferito su sistemi moderni e virtualizzati |
| Windows | `W32Time` | Integrato in AD; il PDC Emulator diventa fonte NTP del dominio |
| Client Linux | `ntpdate`, `chronyc` | Sync manuale o automatico |

### PTP (Precision Time Protocol)

**IEEE 1588-2002**. Sincronizzazione sub-microsecondo su LAN. Usato in:
- Transazioni finanziarie ad alta frequenza.
- Trasmissioni mobile (torri cellulari).
- Array acustici sub-marini.

Meccanismo: scambio di messaggi `Sync` / `Follow_Up` / `Delay_Req` / `Delay_Resp` tra master e slave clock per calcolare offset e latenza di rete.

### Perché l'ora corretta è critica

- **Kerberos**: i ticket hanno validità temporale stretta (±5 minuti); differenze maggiori bloccano l'autenticazione.
- **Log e correlazione**: senza ora sincronizzata, correlare eventi tra sistemi diversi è impossibile.
- **Certificati TLS**: validità legata a timestamp; un orologio scorretto può causare errori SSL.
- **Filesystem e database**: operazioni di journaling e replicazione dipendono dall'ordine temporale degli eventi.

---

## DHCP (Dynamic Host Configuration Protocol)

### Cos'è

Evoluzione di RARP → BOOTP → **DHCP** (RFC 2131). Distribuisce in modo centralizzato la configurazione di rete ai client.

### Parametri distribuiti

- Indirizzo IP (e subnet mask).
- Default gateway.
- Server DNS.
- Nome del dominio.
- Lease time (durata validità).
- Opzioni aggiuntive (NTP server, WINS, PXE boot, ecc.) in formato TLV (Type-Length-Value).

### Handshake DORA

```
Client → Server   DHCP Discover  (broadcast: "chi ha indirizzi?")
Server → Client   DHCP Offer     (unicast/broadcast: "prendi questo IP")
Client → Server   DHCP Request   (broadcast: "accetto questa offerta")
Server → Client   DHCP Ack       (unicast/broadcast: "confermato, lease attivo")
```

### Termini chiave

| Termine | Definizione |
|---------|------------|
| **Scope** | Raggruppamento amministrativo di IP per subnet; il server assegna IP solo da scope configurati |
| **Lease** | Periodo di validità dell'assegnazione; alla scadenza il client rinnova o libera l'IP |
| **Reservation** | Assegnazione statica basata su MAC address; garantisce sempre lo stesso IP a un host specifico |
| **Exclusion** | Range di IP esclusi dall'assegnazione dinamica (per server con IP statici) |
| **Option** | Parametri aggiuntivi in formato TLV distribuiti insieme all'IP |

### Modalità di allocazione

| Modalità | Descrizione |
|----------|------------|
| **Dinamica** | IP assegnato dal pool disponibile; alla scadenza del lease può cambiare |
| **Automatica** | IP assegnato definitivamente al primo DHCP Discover (nessuna scadenza) |
| **Statica (reservation)** | IP sempre lo stesso, legato al MAC address del client |

### Architettura multi-server e relay

**DHCP Relay**: il DHCP opera su broadcast, che non attraversa i router. Per gestire più subnet con un unico server:
1. Un **relay agent** (tipicamente integrato nel router/switch L3) riceve il broadcast del client.
2. Lo incapsula in unicast e lo inoltra al server DHCP centrale.
3. Il server risponde al relay, che lo restituisce al client.

**Ridondanza**: due o più server per subnet con split scope 80/20 (80% degli IP al server primario, 20% al secondario). Windows Server 2012+ supporta configurazione DHCP Failover nativa senza split manuale.

### Implementazioni

| Piattaforma | Software | Note |
|------------|---------|------|
| Linux | `dhcpd` (ISC), `dnsmasq`, `Kea` | `Kea` è il successore moderno di ISC dhcpd |
| Windows | Ruolo DHCP Server | Integrabile con AD; supporta failover nativo da WS 2012 |
| Cloud | Provider-managed | Configurazione limitata; nessuna gestione manuale |
| Embedded | Router/AP consumer | Integrato nel firmware; opzioni limitate |

### Sicurezza

- **DHCP Starvation**: attacco che esaurisce il pool IP inviando DISCOVER con MAC falsi → mitigato con port security sullo switch.
- **Rogue DHCP server**: server non autorizzato che distribuisce configurazioni errate → mitigato con DHCP Snooping su switch gestiti.
- **Autenticazione DHCP**: RFC 3118 (poco implementato); alternativa: registrazione degli IP nel DNS (DDNS integrato).

---

## Articoli correlati

- [[dns-server]]
- [[servizi-infrastrutturali]]
- [[linux-gestione-sistema]]
- [[server-windows]]
- [[linux-log-monitoraggio-rete]]

## Fonti

- `raw/3 - Servizi_COMPILED.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
