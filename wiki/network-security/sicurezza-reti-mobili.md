---
tags: [network-security, GSM, UMTS, LTE, 5G, AKA, IMSI, SIM, USIM, Kasumi, reti-mobili]
data_creazione: 2026-05-31
data_aggiornamento: 2026-05-31
fonti:
  - raw/2026-netsec-2d-wireless_COMPILED.pdf
---

# Sicurezza nelle reti mobili (GSM, UMTS/LTE, 5G)

Le reti mobili cellulari presentano sfide di sicurezza peculiari: il mezzo radio è aperto agli attacchi passivi, i dispositivi sono mobili e hanno risorse limitate, e l'identità dell'utente va protetta. La storia evolutiva 2G→3G→4G→5G mostra una progressiva correzione dei difetti di progettazione.

## Punti chiave

- **GSM (2G)**: autenticazione one-way, algoritmi segreti non pubblicati (security by obscurity), crittografia solo sull'interfaccia radio → fallimento di design.
- **UMTS/LTE (3/4G)**: introduce **AKA** (Authentication and Key Agreement), autenticazione mutua, algoritmi pubblici (Kasumi). La vulnerabilità principale è nella gestione di TMSI/IMSI.
- **IMSI catcher**: attacco pratico e a basso costo che sfrutta l'assenza di protezione dell'identità (TMSI) in 2/3/4G per raccogliere gli IMSI degli utenti.
- **5G**: l'IMSI (chiamato SUPI) non viene mai trasmesso in chiaro; il dispositivo lo cifra con crittografia asimmetrica (SUCI) → risolve l'IMSI catching.
- Il **downgrade attack** permette ancora di far retrocedere un dispositivo 5G a 4G e applicare l'IMSI catching.

## Caratteristiche delle reti wireless mobili

Le reti mobili richiedono attenzione speciale per:
- **Mezzo trasmissivo**: l'aria è aperta agli attacchi passivi; attacchi attivi più difficili ma ancora fattibili.
- **Mobilità**: i meccanismi di sicurezza devono essere veloci per supportare i handoff in tempo reale.
- **Dispositivi limitati**: potenza di calcolo e memoria ridotte → protocolli leggeri.

La tendenza storica (sbagliata) è stata adattare meccanismi di sicurezza pensati per reti cablate → funziona raramente.

## Sicurezza in GSM (2G)

### Architettura GSM (semplificata)

```
MS ──(Uu)──→ BTS ──→ BSC ──→ MSC/VLR ──(SS7)──→ HLR/AuC
                                                     EIR
```

| Componente | Ruolo |
|---|---|
| **MS** (Mobile System) | Il dispositivo mobile con SIM |
| **BTS** (Base Transceiver Station) | Stazione radio |
| **BSC** (Base Station Controller) | Controlla i BTS |
| **MSC/VLR** | Mobile Switching Center / Visiting Location Register (rete visitata) |
| **HLR/AuC** | Home Location Register / Authentication Center (rete home) |
| **EIR** | Equipment Identity Register (stato del terminale per IMEI) |
| **SIM** | Subscriber Identity Module: contiene IMSI, Ki, algoritmi A3/A5/A8 |

### Credenziali di autenticazione GSM

- **IMSI** (International Mobile Subscriber Identity): identità permanente dell'utente, memorizzata nella SIM.
- **Ki** (128 bit): chiave crittografica pre-shared, memorizzata nella SIM e nell'AuC. Non deve essere leggibile.
- **IMEI**: identità del terminale (non della SIM).
- Algoritmi nella SIM: **A3** (autenticazione), **A5** (confidenzialità), **A8** (key generation).

### Protocollo di autenticazione GSM

**Obiettivi**: autenticazione one-way (rete autentica utente), derivazione chiavi effimere.

```
MS          MSC/VLR                HLR/AuC
            ──→ Sec. Info RQST ──→
            ←── Sec. Info RESP     [n vettori AV] ──
            Store AV_1...AV_n, seleziona i
←── Auth. RQST (RAND_i) ───
Compute SRES, Kc                   [SIM usa A3, A8]
──→ Auth. RESP (RES) ────────────→
            Verify RES = SRES_i    [C]
```

**Authentication Vector (AV_i)**: tupla `[RAND_i, SRES_i, Kc_i]` generata dall'AuC:
- **RAND** (128 bit): challenge casuale.
- **SRES** (32 bit): risposta attesa = A3(Ki, RAND).
- **Kc** (64 bit): chiave effimera = A8(Ki, RAND); usata da A5 per cifrare il traffico radio.

Il VLR può eseguire più run di autenticazione senza contattare l'AuC (usa i vettori pre-calcolati).

### Algoritmi crittografici GSM

| Algoritmo | Funzione | Problemi |
|---|---|---|
| **A3** | Autenticazione (MAC) | Basato su COMP128 (segreto); cryptanalizzato nel 1998: chosen-challenge attack rivela Ki |
| **A5** | Confidenzialità (stream cipher) | Deve essere uguale per tutti gli operatori in roaming; A5/1 cryptanalizzato nel 1998/99 |
| **A8** | Key generation | Stessi problemi di A3 |

**COMP128** (implementazione di A3/A8): vulnerabile ad attacchi chosen-text; una volta noto Ki, è banale calcolare Kc → rottura della confidenzialità.

**Due modi per montare l'attacco:**
1. Accesso fisico alla SIM.
2. Fake BTS: l'autenticazione one-way impedisce alla SIM di distinguere una BTS falsa da una reale.

### Criticità di GSM

- **Security by obscurity**: fallisce sempre; gli algoritmi segreti sono stati ugualmente cryptanalizzati.
- **Crittografia solo sull'interfaccia radio** (MS ↔ BSC): il traffico è in chiaro nella parte terrestre.
- **Autenticazione non mutua**: rende possibili fake BTS.
- **Chiavi corte**: Kc è solo 64 bit (almeno è effimera).

## Sicurezza in UMTS/LTE (3/4G)

### Architettura UMTS (semplificata)

```
MS ──→ Node-B ──→ RNC ──→ SGSN ──→ GGSN ──→ Internet
                    └──→ MSC/VLR ──(SS7)──→ HLR/AuC
                                              H-AAA
```

| Componente | Differenza rispetto a GSM |
|---|---|
| **USIM** | User Services Identity Module; evoluzione della SIM |
| **RNC** | Radio Network Controller (sostituisce BSC) |
| **SGSN** | Serving GPRS Support Node (packet switched) |
| **GGSN** | Gateway GPRS Support Node |
| **H-AAA / F-AAA** | Home / Foreign AAA server |

In LTE: **UE** (User Equipment) = MS; **eNodeB** = Node-B evoluto.

### Credenziali UMTS/LTE

Memorizzate nella **USIM**:
- **IMSI**: fisso, leggibile dalla USIM.
- **TMSI** (Temporary Mobile Subscriber Identity): assegnato dalla rete alla prima connessione con IMSI; usato nelle connessioni successive.
- **P-TMSI**: equivalente del TMSI per packet-switched.
- **K** (128 bit): chiave pre-shared tra AuC e USIM; non leggibile dalla USIM.
- Algoritmi **f1–f5, f8–f9**: pubblici e peer-reviewed (a differenza di GSM).

### AKA — Authentication and Key Agreement

**Protocollo di autenticazione mutua** (ISO/IEC 9798-4).

```
MS              SGSN/VLR                HLR/AuC
──→ Attach RQST ──→
  ←── Identity RQST [1] ──
  ──→ Identity RESP (IMSI) [2] ──→
                 ──→ Auth. Data RQST ──→
                 ←── Auth. Data RESP (n AV) ──
                 Store AV_1...AV_n, seleziona i
  ←── Auth. RQST (RAND_i, AUTN_i) ──
Compute RES, XMAC
Verify AUTN [B]
  ──→ Auth. RESP (RES) ──→
                 Verify RES_i = XRES_i [C]
```

**Novità rispetto a GSM:**
- **Autenticazione mutua**: il MS verifica l'AUTN (Authentication Token) generato dall'AuC → protegge dalle fake BTS.
- Il VLR invia `(RAND_i, AUTN_i)` al MS; il MS verifica e risponde con RES.

**Authentication Vector UMTS:** `AV_i = [RAND | XRES | CK | IK | AUTN]`
- **XRES**: risposta attesa (come SRES in GSM, ma variabile 32–128 bit).
- **CK** (Cipher Key, 128 bit): chiave effimera di cifratura.
- **IK** (Integrity Key, 128 bit): chiave effimera di integrità.
- **AUTN = [AK ⊕ SQN | AMF | MAC]**: token di autenticazione della rete.

### AKA: Step (A) — AuC genera gli AV

Funzioni dell'AuC (con K come chiave):
```
f1(K, RAND, SQN, AMF) → MAC    [64 bit]
f2(K, RAND)           → XRES   [32-128 bit]
f3(K, RAND)           → CK     [128 bit]
f4(K, RAND)           → IK     [128 bit]
f5(K, RAND)           → AK     [Anonymity Key]
```

- **SQN** (48 bit): numero di sequenza; garantisce la freschezza e protegge dai replay attack.
- **AK**: protegge l'SQN da attacchi passivi (un attaccante non può tracciare l'utente osservando l'evoluzione di SQN).
- **AMF**: Authentication and key Management Field; gestisce casi speciali.

### AKA: Step (B) — MS verifica l'AUTN

La USIM:
1. Calcola AK = f5(K, RAND), poi recupera SQN = (AK ⊕ SQN) ⊕ AK.
2. Calcola XMAC = f1(K, RAND, SQN, AMF) e verifica MAC = XMAC.
3. Verifica che SQN sia nel range atteso (accetta variazioni di 1–2 per tollerare pacchetti persi).
4. Se XMAC non è valido → **non invia RES** (previene known-text attacks analoghi a GSM).

Tutti gli algoritmi f1-f9 girano **dentro la USIM**, non nell'MS.

### Confidenzialità e integrità in UMTS

**Confidenzialità** (f8 — algoritmo Kasumi in modalità stream):
```
Sender: f8(CK, COUNT-C, BearerID, Direction, Length) → KeyStream
CipherText = PlainText XOR KeyStream
```
- COUNT-C: contatore che evita il riuso del keystream.
- BearerID: identifica il canale radio.
- Direction: keystream diverso per uplink e downlink.

**Integrità** (f9 — algoritmo Kasumi come MAC):
```
f9(IK, COUNT-I, Message, Direction, FRESH) → MAC-I
```
- FRESH: nonce inviato dalla rete prima dell'inizio della cifratura.

**Kasumi**: cifrario a blocchi simmetrico (64 bit, schema Feistel a 8 round), brevettato da Mitsubishi. Usato da f8 e f9. In LTE sostituito da **SNOW3G/AES**.

### Limitazioni di UMTS/LTE: problema TMSI/IMSI

Nonostante la robustezza crittografica, UMTS/LTE è vulnerabile agli **IMSI catcher**:

**Attacco base:**
1. Attaccante monta un rogue eNB che si spaccia per la rete dell'utente.
2. Usa un jammer per disconnettere la MS dalla rete legittima.
3. La MS si riconnette al rogue eNB.
4. Il rogue eNB richiede l'IMSI (le specifiche permettono al network di chiedere l'identità in chiaro).
5. L'attaccante cattura l'IMSI e sparisce.

**Hardware necessario**: SDR (Software Defined Radio) come Ettus B210, BladeRF + software OpenAirInterface o srsLTE. Costo: accessibile.

**Difesa lato rete**: user-assisted detection (measurement report con security values) → specificato in 3GPP 33869. In pratica: **nessuna protezione efficace attualmente**.

## Sicurezza in 5G

### Fix principale: protezione dell'identità

In 2/3/4G il problema dell'IMSI catching non è crittografico ma di design: l'IMSI viene trasmesso in chiaro almeno alla prima connessione.

**Soluzione 5G:**
- L'IMSI è rinominato **SUPI** (Subscription Permanent Identifier).
- Il dispositivo non trasmette mai SUPI in chiaro: lo cifra con crittografia asimmetrica → **SUCI** (Subscription Concealed Identifier).
- Solo la rete home (con la chiave privata corrispondente) può decifrare il SUCI.

### Residua vulnerabilità 5G: downgrade attack

Anche con un dispositivo registrato su 5G, un attaccante può:
1. Fare jamming della rete 5G (gNB).
2. Il dispositivo cade su 4G (eNB disponibile).
3. Fare jamming anche di 4G/5G simultaneamente.
4. Il dispositivo si connette al rogue eNB 4G dell'attaccante.
5. → IMSI catcher applicabile come in 4G.

Il protocollo 5G non protegge contro il downgrade a standard meno sicuri quando la rete legacy è disponibile.

## Articoli correlati

- [[sicurezza-wireless]]
- [[sicurezza-lan]]
- [[protocolli-crittografici]]

## Fonti

- raw/2026-netsec-2d-wireless.pdf
