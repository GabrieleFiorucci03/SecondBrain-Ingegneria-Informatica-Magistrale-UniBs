---
tags: [network-security, PKI, certificati, X509, CA, revoca, CRL, OCSP]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-2a-pki.pdf
---

# PKI — Public Key Infrastructure

Il **problema fondamentale** della crittografia asimmetrica: come autenticare una chiave pubblica? In una rete n-nodi, ogni nodo dovrebbe memorizzare e gestire (n-1) chiavi pubbliche — e come gestire la revoca? La **PKI** risolve questo problema tramite **certificati digitali** firmati da una **Certification Authority (CA)** fidata.

## Punti chiave

- Senza PKI, Trudy può sostituire K_pubA con la propria chiave → MitM non rilevabile.
- Un **certificato crittografico** lega un'identità a una chiave pubblica, garantito dalla firma della CA.
- Per verificare un certificato serve una **copia autentica della chiave pubblica della CA** — stesso problema, scala minore.
- Le root CA usano **certificati self-signed** pre-installati in OS, browser, applicazioni.
- Due meccanismi di revoca: implicito (expiration date) e esplicito (CRL, OCSP).
- Architetture PKI globali in uso oggi: **oligarchia** (molte root CA paritarie) e **anarchia** (PGP — tutti sono CA).
- X.509 è lo standard de facto per la struttura dei certificati.

## Il problema dell'autenticazione delle chiavi pubbliche

**Scenario d'attacco**: Alice invia K_pubA a Bob. Trudy intercetta e sostituisce con la propria K_pubT. Bob crede di parlare con Alice ma sta parlando con Trudy — MitM perfetto.

**Problema di scalabilità** su rete n-nodi: ogni nodo deve memorizzare e aggiornare (n-1) chiavi pubbliche. Come gestire aggiornamenti e revoche?

**Analogia**: un agente di polizia verifica un documento d'identità senza connettersi a sistemi remoti — si fida di *chi ha emesso* il documento, non solo del documento in sé.

**Soluzione**: una terza parte fidata da tutti (CA) firma la coppia {identità, chiave pubblica} → il verificatore controlla la firma della CA usando la K_pubCA (che deve già conoscere in modo autentico).

## Certificati crittografici

**Definizione**: CERT_ID := {ID, K_pubID} — lega un identificatore a una chiave pubblica.

La validità del legame è garantita dalla **firma digitale della CA** su tutto il contenuto del certificato.

```
Verifica: Bob verifica che S_CA(contenuto) sia valida usando K_pubCA
→ se S_CA è valida, Bob sa che la CA ha certificato quel legame ID↔K_pub
```

**Prerequisito**: Bob deve avere una copia *autentica* di K_pubCA (stesso bootstrap problem, scala ridotta).

## PKI — struttura

Una PKI definisce protocolli, policy e meccanismi per garantire l'autenticazione delle chiavi pubbliche. Componenti:

| Componente | Descrizione |
|---|---|
| Formato certificati | Come sono strutturati (X.509) |
| Relazioni tra CA | Come più CA si fidano le une delle altre; come gli utenti si fidano delle CA |
| Gestione ciclo di vita | Emissione, rinnovo, revoca dei certificati |
| Directory services | Come trovare e distribuire i certificati |

## Certificati X.509

Derivato da ITU-T X.500 (directory services per telecom). Specifica tutto ciò che riguarda l'autenticazione. Codificato in **ASN.1**.

### Struttura del certificato

```
┌─────────────────────────────────────────────┐
│  Standard version + altri dati (v1, v2, v3) │  ← versione, validity period, serial number
├─────────────────────────────────────────────┤
│  Identità della CA (Issuer)                 │  ← CN=Test CA, O=UniBS, OU=DII, C=IT
├─────────────────────────────────────────────┤
│  Identità dell'utente (Subject)             │  ← CN=argo.ing.unibs.it, O=UniBS, ...
├─────────────────────────────────────────────┤
│  Chiave pubblica dell'utente                │  ← algoritmo (RSA/DSS/...) + parametri + chiave
├─────────────────────────────────────────────┤
│  Firma della CA su tutto il precedente      │  ← algoritmo (es. SHA-1 enc. RSA) + bytes firma
└─────────────────────────────────────────────┘
```

### Campi principali

| Campo | Dettagli |
|---|---|
| **Versione** | X.509 v1, v2 o v3 (extensioni solo in v3) |
| **Serial number** | La coppia <CA, serial> deve essere unica globalmente |
| **Validity period** | Data inizio e scadenza; **non usare fuori dal periodo** |
| **Issuer / Subject** | Formato X.500: CN (Common Name, identificatore primario), O=Organization, OU=Org Unit, C=Country, ST=State, L=Locality |
| **Public key** | Il cryptosystem (RSA, DSS...) + parametri (per RSA: n e e) + la chiave |
| **Estensioni (v3)** | Opzionali; specificano uso permesso del certificato |
| **Firma CA** | Algoritmo + bytes (es: MDC(tutto) cifrato con K_privCA) |

## Distribuzione della K_pubCA

Per verificare un certificato serve K_pubCA — stesso problema bootstrap, scala ridotta.

**K_pubCA è distribuita come CERT_CA:**
- Se la CA è una **root CA**: **certificato self-signed** (Issuer = Subject, CA firma se stessa)
- Altrimenti: certificato emesso da un'altra CA (catena di trust)

**Soluzione pratica attuale**: decine di certificati di root CA pre-installati in:
- Sistemi operativi (OS)
- Browser web, email client
- Smartphone

**Problema aperto**: mancanza di un sistema globale efficiente di directory e policy per le gerarchie di CA.

## Distribuzione dei certificati utente

**Ideale**: directory service analogu al DNS — certificati scaricabili on-demand senza scambiarli direttamente. Oggi implementato solo a livello locale con **LDAP**.

**Pratica attuale**: le applicazioni si scambiano i certificati all'inizio del run di autenticazione (es. durante TLS handshake).

## Revoca dei certificati

**Quando revocare**: chiave privata compromessa (rubata), chiave persa, chiave non più in uso (cambio di lavoro).

**Due meccanismi:**

| Tipo | Descrizione |
|---|---|
| **Implicita** | Expiration date: il certificato scade automaticamente |
| **Esplicita** | La CA revoca il certificato prima della scadenza |

### CRL — Certificate Revocation List

La CA emette periodicamente la CRL: lista di certificati revocati (prima della scadenza).

**Contenuto CRL:**
- Issuer, data ultimo aggiornamento, data prossimo aggiornamento
- Lista di serial number revocati con data di revoca
- Firma digitale della CA sull'intero CRL

**Problemi:**
- Ogni utente dovrebbe scaricare periodicamente le CRL di tutte le CA di cui si fida (oneroso)
- Se la CRL è scaduta e la CA irraggiungibile → non usare i certificati emessi da quella CA
- **Molte implementazioni oggi saltano questi controlli**

### OCSP — Online Certificate Status Protocol

RFC 6960 (2013). Permette di verificare lo stato di un singolo certificato on-line su richiesta. Firefox lo usa. Più efficiente delle CRL per verifiche singole.

### Meccanismi proprietari

- **CRLSets** (Chrome): browser scarica periodicamente un CRL curato da Google
- **OneCRL** (Firefox): analogo, mantenuto da Mozilla

## Architetture PKI

| Modello | Struttura | Note |
|---|---|---|
| **Monopolio** | Una sola CA globale (o gerarchia sotto una root) | Impossibile politicamente |
| **Oligarchia** | Molte root CA allo stesso livello | **In uso oggi** (TLS sul web) |
| **Oligarchia/gerarchia** | Alcune root CA con delega di namespace ad altre CA | Simile a DNS; obiettivo futuro |
| **Anarchia** | Nessuna CA centrale; tutti possono certificare | **In uso oggi**: modello PGP (web of trust) |

Oggi solo **oligarchia** e **anarchia** funzionano a scala globale. Sforzi in corso per implementare strutture gerarchiche basate su **certificate chains** (catene di certificati verso una o più root CA).

## Gestione di una CA

**K_privCA è il bene più prezioso**: la sicurezza dell'intero sistema dipende da essa.
- La CA e la sua K_privCA devono essere mantenute **offline**, disconnesse da Internet (o da qualsiasi rete)
- Distribuire CERT_CA in modo che garantisca integrità e autenticità

**Identificazione degli utenti prima dell'emissione:**
- La CA deve accertarsi dell'identità di A e verificare che K_pubA sia effettivamente di A
- Policy molto variabile tra CA: email di conferma (per utenti normali) o verifica con autorità legali (per usi critici)
- Mancanza di procedure standardizzate globali

## Chi usa X.509

- Qualsiasi protocollo di autenticazione con chiavi asimmetriche (es. STS)
- **SSL/TLS** → [[tls]]
- **IPSec**
- Metodi EAP in reti 802.11 e 802.3 → [[sicurezza-lan]] [[sicurezza-wireless]]
- **S/MIME** (email cifrate e firmate)

## Articoli correlati

- [[crittografia-asimmetrica-fondamenti]]
- [[rsa]]
- [[diffie-hellman]]
- [[protocolli-crittografici]]
- [[tls]]

## Fonti

- raw/2026-netsec-2a-pki.pdf
