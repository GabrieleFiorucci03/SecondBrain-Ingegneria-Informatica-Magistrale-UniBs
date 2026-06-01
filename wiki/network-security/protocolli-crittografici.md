---
tags: [network-security, protocolli, autenticazione, challenge-response, PAP, CHAP, EKE, zero-knowledge]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-1e-protocols.pdf
---

# Protocolli Crittografici e Autenticazione

Un **protocollo crittografico** è un algoritmo distribuito tra n≥2 entità che specifica le azioni necessarie per raggiungere un obiettivo di sicurezza (autenticazione, confidenzialità, integrità). Il protocollo dice *come usare* un algoritmo crittografico.

## Punti chiave

- **Message authentication** (MAC, firma digitale): non richiede real-time — la verifica può avvenire in futuro.
- **Entity authentication**: deve avvenire in real-time — si verifica l'identità di chi partecipa *ora* al protocollo.
- Tre requisiti fondamentali: autenticazione, non-trasferibilità, robustezza contro false identità.
- **PAP e CHAP**: protocolli deboli — **non usarli mai** su canale non cifrato.
- I challenge-response forti usano segreti crittografici (chiavi simmetriche o keypair asimmetrici), mai password semplici.
- I challenge devono essere time-variant (nonce, sequence number, timestamp) per resistere ai replay attack.
- **EKE** è l'unico protocollo password-based davvero forte: autentica DH con la password, resistente agli attacchi offline.

## Tipi di autenticazione

| Tipo | Goal | Timing | Strumento |
|---|---|---|---|
| Message authentication | Proof of origin + integrity | Non real-time | MAC (no non-ripudio), firma digitale |
| Entity authentication | Identità del partecipante | Real-time | Protocol run |

**Autenticazione mutuale**: sia A autentica B, sia B autentica A con lo stesso processo.

## Requisiti di un protocollo di autenticazione

1. **Autenticazione**: A riesce ad accertare l'identità di B con prove
2. **Non-trasferibilità**: A non può riusare i dati dello scambio con B per impersonare B con una terza parte C
3. **Robustezza contro false identità**: C non riesce a impersonare B anche se:
   - C ha osservato più run di autenticazione tra A e B
   - C ha eseguito run con A, B o entrambi
   - Più istanze girano concorrentemente

## Tre classi di protocolli

| Classe | Descrizione |
|---|---|
| **Password-based** | Weak (password statica, vulnerabile) o strong (EKE) |
| **Challenge-response** | B chiede ad A di trasformare una challenge con il suo segreto s — A non deve rivelare s |
| **Zero-knowledge proof** | Il verifier non apprende nulla che violi la non-trasferibilità |

## Protocolli password-based deboli

### PAP — Password Authentication Protocol

Usato quasi esclusivamente in PPP (al di fuori di tunneled-EAP).

```
Alice → AS: {A, pwd}
AS: lookup(A) → {salt, h}; verifica h = f(pwd|salt)
```

**Problemi:**
- Man-in-the-middle (canale non autenticato)
- Richiede canale sicuro (ipotesi irrealistica fuori da PPP)
- Trasferibilità: AS può impersonare Alice verso terzi
- Dictionary attack sul database delle password
- Il salt riduce ma non elimina i dictionary attack (uno per ogni valore di salt)

**Regola: non usare PAP (mai, a meno che non sia dentro un tunnel cifrato).**

### CHAP — Challenge Handshake Authentication Protocol

Usato in PPP e MS Windows. Migliore di PAP, ma ancora debole.

```
Alice → AS: {Alice}
AS → Alice: c (challenge casuale)
Alice → AS: r = f(pwd, c) = MDC(user|pwd|c)  [CHAP usa MD5]
AS: verifica r' = f(pwd, c) = r?
```

**Problemi:**
- Dictionary attack su r (Trudy può sniffare e attaccare offline)
- Il database password dell'AS è in cleartext
- Replay attack se c non è un buon numero casuale

**Regola: non usare CHAP (mai, a meno che non sia dentro un tunnel cifrato).**

### Lamport's Hash — One-time passwords (SKIPPED nel corso)

Protocollo semi-debole: f^n(pwd) — ogni run usa una pre-immagine diversa. Proprietà: non-trasferabilità limitata; problemi: MitM, non-mutuale.

## EKE — Encrypted Key Exchange (Strong password)

Bellovin & Merritt. Idea: **autenticare uno scambio DH con una password**.

```
Alice: w = f(pwd);  A = E_w(g^Sa mod p)
Alice → Bob: {Alice, A, g, p}

Bob:  w = f(pwd);  c₁ = random();  K = g^(SaSb) mod p;  B = E_w(g^Sb mod p, c₁)
Bob → Alice: {Bob, B}

Alice: decifra B → K = g^(SbSa) mod p; c₂ = random()
Alice → Bob: E_K(c₁, c₂)

Bob → Alice: E_K(c₂)
```

**Proprietà:**
- Mutual authentication
- Resistenza agli attacchi offline (decifrare A o B con la w corretta dà un numero random, non verificabile)
- Attacco online possibile (Trudy finge di essere Bob) ma solo un tentativo; Alice detecta immediatamente (B sbagliato)
- Ephemeral key setup

**Problema**: se AS è il server, il database delle password è in cleartext.

## Protocolli Challenge-Response

Schema generale: B sfida A a trasformare una challenge c con il suo segreto s; A risponde senza rivelare s.

**Requisiti per protocolli forti:**
- s deve essere un segreto crittografico (chiave simmetrica o chiave privata) — **non una password**
- Challenge time-variant → resistenza ai replay attack
- Il segreto non viene mai rivelato durante il run

### Tipi di challenge

| Tipo | Descrizione | Costo |
|---|---|---|
| **Nonce** | Numero usato solo una volta; implementato con PRNG | Basso |
| **Sequence number** | Contatore monotono crescente | Richiede sincronizzazione di stato |
| **Timestamp** | Challenge implicita; clocks devono essere sincronizzati | Richiede NTP o simile |

### Con algoritmi simmetrici (ISO/IEC 9798-2/4)

Segreto s = chiave simmetrica condivisa tra A e B (o tra A e un Authentication Server Z).

**Variante timestamp** (one-way, massima efficienza di rete):
```
Alice → Bob: {A, R = E_s(t, B)}
Bob: {t', B'} = D_s(R);  verifica t-x < t' < t+x;  verifica B' = B
```

**Variante nonce** (mutual, c₁ da Bob, c₂ da Alice):
```
Bob → Alice:  {c₁, B}
Alice → Bob:  {A, c₂, R_A = E_s(c₁, c₂, B)}
Bob → Alice:  R_B = E_s(c₂, c₁, A)
```
c₂ inviato da Alice previene chosen-text attacks su Bob.

**Variante MAC** (stessa struttura, R_A = MAC_s(c₁,c₂,B)):
```
Bob → Alice:  {c₁, B}
Alice → Bob:  {A, c₂, R_A = MAC_s(c₁,c₂,B)}
Bob → Alice:  R_B = MAC_s(c₂,c₁,A)
```

**Dove memorizzare s**: di difficile memorizzazione → dispositivi hardware dedicati (cryptographic calculator, **SecurID**). Le banche stanno abbandonando SecurID per MitM e MitB (Man-in-the-browser); stessa soluzione: usare un secondo dispositivo/canale.

### Con algoritmi asimmetrici

Segreto s = keypair; variante con certificati generati da una **CA** (Certification Authority → [[pki]]).

**Esempio basato su decifratura** (one-way):
```
Alice → Bob: {A}
Bob → Alice: {MDC(c), B, E_K_pub_Alice(c, B)}
Alice → Bob: {c}
Bob: verifica c ricevuto = c nel messaggio cifrato
```
MDC(c) contrastare chosen-text attacks.

**Regola d'oro**: usare chiavi diverse per scopi diversi, oppure assicurarsi che la libreria crittografica implementi uno standard sicuro (PKCS).

## Attacchi ai protocolli di autenticazione

### Snooping e database reading
- **Snooping**: Trudy osserva run del protocollo → di solito primo passo di qualsiasi attacco; può montare un **replay attack** riusando informazioni di run precedenti
- **Contromisura**: usare quantità time-variant e identity-variant (includi l'identità di Bob nelle challenge timestamp-based)
- **Database reading**: Trudy legge il database delle credenziali (di A o B) → accesso fisico al sistema

### False identity (Chosen-text attack)
- Trudy finge di essere Bob, chiede ad Alice di trasformare challenge specifiche → con crittografia simmetrica cerca di derivare s; con asimmetrica cerca di far firmare ad Alice dati arbitrari
- **Contromisure:**
  - Usare quantità time-variant scelte da ciascun partecipante
  - Collegare le quantità usate da ciascun partecipante (ordine di c₁ e c₂ non è casuale!)
  - Includere random data nella trasformazione delle challenge: `BAD: R=S_A(c₁)` → `GOOD: R=S_A(c₁, random())`
  - Usare chiavi diverse per scopi diversi (firma, autenticazione, cifratura)

### Reflection attack
- Trudy avvia più run in parallelo fingendo di essere Alice; usa la risposta di Bob in un run per rispondere nell'altro run
- **Contromisure:** usare chiavi diverse nelle due direzioni; non permettere ad Alice di iniziare il protocollo; evitare simmetrie nei messaggi: `BAD: {MAC_k(c₁), c₂}` → `GOOD: {MAC_k(c₁,c₂), c₂}`

### Interleaving attack
- Trudy avvia più run in parallelo, fingendo di essere Bob verso Alice e Alice verso Bob, combinando risposte tra i due run
- **Contromisura:** collegare sequenzialmente le challenge nel run: `BAD: MAC_k(c₂,c₄)` → `GOOD: MAC_k(c₂,c₁)`

## Multi-factor authentication (MFA / 2FA)

**Motivazione:** crescente numero di data breach, phishing, keylogging, credential stuffing — la singola password non è sufficiente.

**Tre fattori:**
| Fattore | Tipo | Esempi |
|---|---|---|
| **Knowledge** | Sapere | Password, PIN |
| **Possession** | Avere | Hardware token, codice SMS, mobile app |
| **Inherence** | Essere | Impronta, riconoscimento facciale |

Combinare fattori da categorie diverse aumenta la sicurezza. Due fattori = **2FA**; più di due = **MFA**.

**Come funziona 2FA**: utente inserisce username+password → sistema richiede secondo fattore (su dispositivo diverso) → accesso concesso.

**Implementazioni pratiche**: OTP time-based (TOTP) e counter-based (HOTP); challenge-response o certificati; software token (Google Authenticator); hardware token (RSA SecurID); sistemi biometrici.

## Chiavi effimere e session key setup

### Perché usare chiavi effimere (session keys)

- Cambiare chiave frequentemente limita il ciphertext disponibile all'attaccante
- Se una session key è compromessa, solo i dati di quella sessione sono esposti
- Dalla stessa master key si possono derivare chiavi diverse per sessioni diverse e con persone diverse

**Chiave effimera** (K_t): usata per una singola sessione. **Master key**: persiste più a lungo di una singola sessione.

**Requisito**: K_t deve essere **esplicitamente autenticata** — garanzia oggettiva che solo le parti attive e autenticate la conoscono.

### K_t trasportata vs K_t derivata

| Tipo | Descrizione | PFS | Replay | Complessità |
|---|---|---|---|---|
| **Trasportata** | A (o B) genera K_t e la trasmette protetta dalla master key | No | Difficile da contrastare | Bassa |
| **Derivata** | A e B si scambiano quantità per calcolare insieme K_t (DH) | Possibile | Più facile da contrastare | Alta |

**K_t trasportata** (esempio):
```
Alice: K_t = random()
Alice → Bob: E_K(K_t, B)          [include B per evitare replay verso Alice]
Problema: replay verso Bob; no PFS
Variante con nonce: E_K(K_t, B, n_bob)
Variante con firma: E_K_bob(t, K_t, S_K_privA(t, K_t, B))
```

**K_t derivata** (esempio):
```
Bob → Alice: n_b (nonce)
Alice → Bob: E_K(r_a, n_a, n_b, B)   [r_a contribuisce a K_t]
Bob → Alice: E_K(r_b, n_b, n_a, A)   [r_b contribuisce a K_t]
K_t = MDC(r_a | r_b)   — Non offre PFS (K_t derivata da K_master)
```

### STS — Station To Station (DH autenticato)

Variante DH con autenticazione tramite keypair/certificati. **Offre PFS**.

```
Alice → Bob: g, p, g^Sa mod p
Bob → Alice: g^Sb mod p, E_Kt(S_K_privB(g^Sb, g^Sa))
Alice → Bob: E_Kt(S_K_privA(g^Sa, g^Sb))
K_t = g^(Sa·Sb) mod p
```

- Le firme cifrate con K_t confermano: che entrambe le parti hanno K_t corretta; che chi ha co-generato K_t è lo stesso che ha creato la firma
- **Cons**: computazionalmente oneroso (DH + firme digitali)
- **PFS**: sì, purché Sa, Sb, K_t vengano cancellati dopo la sessione

### Perfect Forward Secrecy (PFS)

**Definizione**: un protocollo offre PFS se compromettere la master key al tempo t **non permette di derivare alcuna session key generata fino a t-1**.

"Forward": compromettere la master key *dopo* che una sessione S è terminata non compromette S.

**Esempi:**
- EKE offre PFS se A e B cancellano Sa, Sb, K dopo la sessione
- Nessun meccanismo che trasporti K_t cifrato con la master key può offrire PFS
- Compromettere la master key al tempo t compromette comunque tutte le sessioni *future*

**Forward secrecy** (più debole): compromettere la session key K_t di una sessione non compromette le session key di sessioni passate o future.

### Resistenza a known-key attack

Se Trudy compromette una o più session key passate, non deve poter:
- Compromettere session key future
- Derivare la master key
- Impersonare Alice o Bob

Importante perché: le session key sono spesso considerate meno importanti delle master key (più probabilità di compromissione) e cifrano grandi quantità di dati (più vulnerabili alla crittoanalisi).

## Articoli correlati

- [[introduzione-crittografia]]
- [[crittografia-simmetrica]]
- [[funzioni-hash]]
- [[rsa]]
- [[diffie-hellman]]
- [[pki]]
- [[tls]]

## Fonti

- raw/2026-netsec-1e-protocols.pdf
