---
tags: [network-security, wireless, 802.11, WEP, WPA, WPA2, WPA3, 4WHS, CCMP, TKIP, KRACK]
data_creazione: 2026-05-31
data_aggiornamento: 2026-05-31
fonti:
  - raw/2026-netsec-2c2-wlan_COMPILED.pdf
---

# Sicurezza nelle WLAN (802.11 / WEP / WPA)

Le reti wireless 802.11 usano l'aria come mezzo trasmissivo, esponendo il traffico a chiunque sia nel raggio radio. La storia della sicurezza nelle WLAN è un caso studio di design errato (WEP) seguito da una progressiva correzione (WPA → WPA2 → WPA3).

## Punti chiave

- WEP è **completamente insicuro** e non deve essere usato: 5 vulnerabilità strutturali note.
- **802.11i** (WPA/WPA2/WPA3) risolve i problemi di WEP usando 802.1x+EAP e cipher suite moderne.
- Il **Four-Way Handshake (4WHS)** deriva le chiavi effimere (PTK) dalla PMK concordata in fase di autenticazione.
- **TKIP** (WPA) è deprecato dal 2009; **CCMP** (WPA2, basato su AES) è lo standard; **GCMP** è il più moderno.
- L'attacco **KRACK** (2017) sfrutta la reinstallazione delle chiavi nel 4WHS forzando il riuso dei nonce.
- **WPA3** corregge la mancanza di PFS (tramite SAE) e gli evil-twin (tramite SAE-PK).

## Architettura 802.11

**Componenti principali:**
- **Access Point (AP)**: termina l'interfaccia radio; fa da bridge tra lato wired (802.3) e wireless (802.11).
- **STA (Station)**: il client.

**Modalità operative:** Infrastructure mode (con AP) e Ad-hoc mode (senza AP).

**5 passi per accedere alla rete:**
1. Scan per AP (passivo: beacon AP; attivo: STA probe + beacon AP)
2. Authentication (802.11 layer-2)
3. Association
4. Authentication[2] (802.1x/EAP, la vera autenticazione di rete)
5. DHCP

## Classi di meccanismi di sicurezza

| Livello | Meccanismo | Note |
|---|---|---|
| Nessuna | Open mode | Nessuna protezione |
| Non-crypto | Closed mode (SSID nascosto) | SSID visibile nei probe; nessuna sicurezza reale |
| Non-crypto | MAC ACL | Facilmente aggirabile cambiando il MAC |
| Crypto | WEP | Completamente rotto |
| Crypto | 802.11i (WPA/WPA2/WPA3) | Standard sicuro attuale |

## WEP — Wired Equivalent Privacy

**Obiettivi**: autenticazione, confidenzialità, integrità a livello 2.

**Componenti principali:**
- Chiave pre-shared **K** (40 o 104 bit), condivisa tra tutte le STA e gli AP della WLAN.
- **RC4** (stream cipher) come unica primitiva crittografica.
- **CRC-32** (non-crypto) per l'integrità.
- **IV** (Initialization Vector): 24 bit, cambia per ogni pacchetto per generare keystream diversi.

**Protocollo di autenticazione WEP:**
```
STA → AP: MsgAuth, StationID, AuthAlgo="Shared Key"
STA ← AP: MsgAuth, AuthAlgo="Shared Key", Challenge=WEP_String
STA → AP: MsgAuth, Response=E_K(WEP_String)   [con RC4]
STA ← AP: MsgAuth, Response=Success/Fail
```
L'autenticazione è **unidirezionale** (solo il client si autentica alla rete).

**Schema di cifratura WEP:**
```
Seed = K | IV
KeyStream = RC4(Seed)
ICV = CRC-32(Plaintext)
CipherText = KeyStream XOR (Plaintext | ICV)
Pacchetto = [IV | CipherText | ICV]
```

### Problemi strutturali di WEP

**Problema 1 — Group key:**
- Tutti i nodi condividono la stessa K → ogni utente può decifrare il traffico degli altri.
- La stessa chiave a lungo termine è usata per autenticazione, confidenzialità e integrità.
- Gestione delle chiavi impossibile (cosa succede quando un utente lascia la rete?).

**Problema 2 — RC4 usato incorrettamente:**
- IV space troppo piccolo (24 bit = ~16M valori). Paradosso del compleanno: collisione al 50% con ~4000 pacchetti.
- Su un AP trafficato, il collisione avviene ogni minuto circa.
- Quando due IV collidono → stesso keystream → attacchi known-plaintext per costruire dizionari `[IV, keystream]`.

**Problema 3 — Protocollo di autenticazione:**
- Un attaccante passivo intercetta `[IV, Challenge, E_K(Challenge)]` → ottiene un keystream valido.
- Può autenticarsi riusando quel `[IV, keystream]` senza conoscere K.
- Un rogue AP può far autenticare i client a sé e raccogliere molte coppie `[IV, keystream]`.

**Problema 4 — CRC-32 è lineare:**
- CRC-32 è lineare: `CRC(A XOR B) = CRC(A) XOR CRC(B)`.
- Un attaccante può modificare il ciphertext e aggiornare il CRC senza conoscere K.
- → Bit-flipping attack: si può cambiare un bit del plaintext senza che l'AP se ne accorga.

**Problema 5 — IV deboli:**
- Alcune coppie `(IV, K)` sono *weak* e producono keystream correlati alla chiave.
- Analisi di 1M+ pacchetti WEP: ~90% di probabilità di recuperare K.
- Tool: AirSnort, WEPCrack.

**Conclusione**: WEP non offre **nessuna** protezione reale. Dà un falso senso di sicurezza, che è peggio di nessuna sicurezza.

## 802.11i — WPA / WPA2 / WPA3

**802.11i** è lo standard di sicurezza corrente per 802.11, progettato da esperti di crittografia.

### Building blocks

**Autenticazione e scambio di chiavi:**
- **EAP + 802.1x** ("WPA Enterprise"): usa un Authentication Server (AS) esterno; funziona con tutte le reti 802-based.
- **PSK** ("WPA Personal"): Pre-Shared Key tra STA e AP; nessun server esterno; adatto per uso domestico.

**Cipher suite per confidenzialità e integrità:**

| Suite | Standard | Descrizione |
|---|---|---|
| **TKIP** | WPA (2002) | RC4 riusato correttamente; backward compatible con HW WEP; **deprecato dal 2009** |
| **CCMP** | WPA2 (2004) | AES-128 in CTR+CBC-MAC; obbligatorio in 802.11i; standard sicuro corrente |
| **GCMP** | WPA2 (2012) | AES-CTR + GHASH; più veloce di CCMP; usato in 802.11ad/ax |

Cipher suite e modalità di autenticazione sono negoziate durante l'associazione.

### Flusso di autenticazione 802.11i (Enterprise)

```
STA           AP (relay EAP)       AS (Authentication Server)
  ←── Discover AP, Null Auth., Association ───
  ←── 802.1X/EAP-RQST(ID) ────
  ──→ 802.1X/EAP-RESP(ID) ────→ EAP-ACCESS-RQST(ID) ──→
                    ←─── EAP (mutual) auth. (method-specific) ───→
      Derive PMK                               Derive PMK
                    ←─── EAP-ACCEPT(PMK) ───────────────────────
  ←── 802.1X/EAP-Success ─────
  ←──────── 4WHS ────────────→
```

La **PMK** (Pairwise Master Key) è la chiave effimera principale: tutte le altre chiavi effimere derivano da essa.

### Four-Way Handshake (4WHS)

Il 4WHS autentica mutuamente STA e AP, e deriva la **PTK** (Pairwise Transient Key) dalla PMK.

```
AP                    STA
   ←── ANonce [1] ──────
   Derive Snonce, keys=PRF(PMK, Nonces, MAC_addresses)
   ──→ SNonce, MIC_KCK [2] ──→
       keys=PRF(PMK, Nonces, MAC_addresses)
   ←── Install, ANonce, E_KEK[GTK], MIC_KCK [3] ──
   ──→ Install, MIC_KCK [4] ──→
```

La PTK è composta da:
- **KCK** (Key Confirmation Key): protegge i messaggi del 4WHS.
- **KEK** (Key Encryption Key): cifra il GTK nel messaggio 3.
- **TK** (Temporal Key): protegge i frame dati.

La **GTK** (Group Temporal Key) protegge il traffico multicast/broadcast; deriva dalla GMK generata dall'AP all'avvio.

**Nota**: il 4WHS non supporta PFS — WPA3 lo corregge.

### TKIP — Temporal Key Integrity Protocol

TKIP usa RC4 ma lo impiega correttamente per ovviare ai problemi WEP:
- IV esteso a **48 bit** (da 24), trattato come numero di sequenza.
- Chiave per-pacchetto derivata da: `Base_Key (128 bit) | TA (48 bit) | IV (48 bit)`.
- Nessun IV viene mai passato direttamente a RC4 → eliminati gli IV deboli.
- Integrità: algoritmo **Michael** (MAC key-based) + CRC-32 come ICV residuale.

**Problema di sicurezza**: Michael è invertibile — conoscendo plaintext+MIC+ciphertext si può recuperare la MIC key → possibile forging di messaggi. Non usare TKIP.

### CCMP — Counter-mode with CBC-MAC Protocol

CCMP è basato interamente su **AES-128**:
- **CTR mode** per la confidenzialità: keystream = `E_TK(IV || contatore)`, poi XOR col plaintext.
- **CBC-MAC mode** per l'integrità: ogni blocco del messaggio viene XORato col risultato del round precedente e cifrato con la MIC key; si conservano i 64 bit finali.

**GCMP** (Galois/Counter Mode Protocol): usa AES-CTR per confidenzialità e GHASH per l'integrità in un'unica passata. Più veloce, ma in caso di KRACK è più vulnerabile (in teoria si può recuperare la chiave).

### Gestione degli IV in 802.11i

- IV esteso da 24 a 48 bit (~10^15 valori).
- Gli IV sono **numeri di sequenza**: il ricevitore rifiuta pacchetti con `IV ≤ IV_visto_di_recente` (anti-replay).
- IV resettato a 0 ad ogni nuovo 4WHS.

## Vulnerabilità WPA/WPA2 — KRACK Attack (2017)

**KRACK** (Key Reinstallation Attacks, Vanhoef e Piessens, ACM CCS 2017) sfrutta un bug di implementazione del 4WHS.

**Principio:**
- L'attaccante esegue un MITM replicando l'AP su un canale diverso.
- Blocca il messaggio 4 del 4WHS → l'AP ritrasmette il messaggio 3.
- Il supplicant (STA) reinstalla la PTK e **azzera il contatore IV** al momento della reinstallazione.
- I frame successivi riusano lo stesso IV → attacco nonce-reuse.

**Impatto per cipher suite:**
- **TKIP**: decifratura + forging di messaggi.
- **CCMP**: decifratura (al più replay).
- **GCMP**: in teoria si può recuperare la chiave.

**Vulnerabilità aggiuntive WPA/WPA2:**
- **PSK**: chi conosce la passphrase può fare evil-twin (utenti non si accorgono) o decifrare il traffico catturato passivamente (no PFS).
- **Enterprise**: configurazioni scorrette permettono di impersonare la rete o rubare le password utente.

## WPA3

WPA3 risolve due problemi principali:

**1. PFS in reti domestiche (SAE — Simultaneous Authentication of Equals):**
- Sostituisce il PSK diretto nella derivazione della PMK.
- SAE usa ECDH per derivare una PMK fresca ad ogni connessione → PFS garantito.
- Flusso: PSK fisso → SAE exchange (commit + confirm) → nuovo PMK → 4WHS con PMK fresca.

**2. Evil-twin in reti grandi (SAE-PK — Public Key):**
- La passphrase riflette una coppia chiave pubblica/privata installata su tutti gli AP.
- Durante SAE, le STA possono verificare l'autenticità dell'AP.
- Un evil-twin con la stessa passphrase non può autenticarsi perché non conosce la chiave privata.

## Articoli correlati

- [[sicurezza-lan]]
- [[protocolli-crittografici]]
- [[tls]]
- [[pki]]
- [[sicurezza-reti-mobili]]

## Fonti

- raw/2026-netsec-2c2-wlan.pdf
