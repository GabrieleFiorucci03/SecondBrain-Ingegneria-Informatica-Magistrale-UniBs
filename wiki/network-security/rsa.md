---
tags: [network-security, RSA, crittografia-asimmetrica, firma-digitale, fattorizzazione]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-1d2-asymmetric-crypto-rsa.pdf
---

# RSA — Rivest, Shamir, Adleman

**RSA** (1977, pubblicato 1978) è il più antico sistema crittografico asimmetrico ancora in uso. Sicurezza basata sulla **difficoltà di fattorizzare** n=p·q in fattori primi. Si usa per confidenzialità, firme digitali e key derivation.

*Nota storica: C. Cocks del CESG britannico inventò lo stesso meccanismo nel 1973, ma rimase classificato.*

## Punti chiave

- **Problema di base**: dato n=p·q, trovare p e q è computazionalmente infeasibile (migliore algoritmo: sub-esponenziale, migliaia di MIPS-year per 512 bit).
- **Parametri chiave**: K_pub={e,n}, K_priv={d,n}; d=e⁻¹ mod φ(n); e spesso è una costante piccola (3, 17, 65537).
- **Dimensione minima di n**: 1024 bit per resistere a tecniche di fattorizzazione moderne.
- **In pratica RSA si usa solo su stringhe brevi** (~200 bit) — per dati lunghi si usa RSA per scambiare una chiave simmetrica effimera.
- **PKCS (Public Key Cryptography Standards)**: risolve vulnerabilità di RSA con padding random obbligatorio.
- Tre attacchi principali: brute-force su d, fattorizzazione di n, man-in-the-middle sulla chiave pubblica.

## Parametri RSA

| Parametro | Tipo | Calcolo | Note |
|---|---|---|---|
| p, q | Privato | Generati casualmente, 512+ bit | Primi grandi; P(x primo) ≈ (ln x)⁻¹ ≈ 1/230 per 512 bit |
| n = p·q | Pubblico | n = p·q | Block size b: 2^b < n < 2^(b+1) |
| φ(n) | Privato | (p-1)·(q-1) | Calcolare φ(n) equivale a fattorizzare n |
| e | Pubblico | Scelto coprimo con φ(n) | Spesso 3, 17 o 65537 (2^16+1) |
| d | Privato | d = e⁻¹ mod φ(n) | Calcolato con algoritmo euclideo esteso |
| K_pub | Pubblico | {e, n} | Distribuita liberamente |
| K_priv | Privato | {d, n} | Mai rivelata |

## Algoritmo (tre usi)

### Confidenzialità
```
Cifratura (Bob → Alice, con K_pub di Alice):
  c = m^e mod n

Decifratura (Alice):
  m = c^d mod n = m^(ed) mod n = m^(1 mod φ(n)) mod n = m  [Teorema di Eulero]
```

**Vincolo**: m < n (da cui il requisito 2^b < n).

**Problema aperto**: come Bob autentica la chiave pubblica di Alice? → [[pki]]

### Firma digitale
```
Firma (Alice con K_priv):
  f = m^d mod n    (o f = MDC(m)^d mod n)

Verifica (Bob con K_pub di Alice):
  m' = f^e mod n   → verifica m' = m
```

In pratica si firma il **digest** h(m) per evitare il limite di b bit: Alice manda {m, h(m)^d mod n}. Bob verifica h(m') = (firma)^e mod n.

**Non-ripudio**: il verificatore non ha bisogno della chiave privata del firmatario.

### Ephemeral key setup (uso pratico principale)
```
1. Bob genera r casuale (r < n)
2. Bob calcola c = r^e mod n, invia c ad Alice
3. Alice calcola r = c^d mod n
4. Alice e Bob usano r come chiave simmetrica per la sessione
   (es. DES_r o AES_r per i messaggi successivi)
```

Questo è il **modo pratico principale** di usare RSA per la confidenzialità: i cifrari simmetrici sono molto più efficienti.

## Generazione della keypair

**Algoritmo (es. con e=3):**
1. Scegli e (es. 3)
2. Genera casualmente un numero dispari y di 512+ bit, poni x = 3y+2 (così x-1 è coprimo con e=3)
3. Verifica che x sia primo (prima 100 piccoli primi; poi test Miller-Rabin o deterministico AKS)
4. Se non primo, torna a 2
5. Poni q=x, ripeti per trovare p
6. n = p·q, φ(n) = (p-1)(q-1)
7. d = e⁻¹ mod φ(n) (algoritmo euclideo esteso)
8. K_pub={e,n}, K_priv={d,n}

**Test di primalità:**
- **Fermat's little theorem**: se x primo → y^(x-1) ≡ 1 mod x; ma i numeri di Carmichael lo soddisfano anche senza essere primi → non deterministico
- **Miller-Rabin**: non deterministico ma molto accurato
- **AKS** (Agrawal-Kayal-Saxena, 2002): deterministico, polinomiale

## Sicurezza e attacchi

### Attacchi passivi

| Attacco | Descrizione | Difficoltà |
|---|---|---|
| Brute-force su d | Ricerca esaustiva di d | Più difficile di fattorizzare n |
| Fattorizzare n | Trovare p,q da n | Sub-esponenziale; migliaia MIPS-year per 512 bit |
| Calcolare φ(n) | Equivalente a fattorizzare n | Provato |
| Plaintext piccolo (e=3) | Se m < ³√n, allora m³ mod n = m³ → Trudy calcola ³√c | **Soluzione: PKCS padding** |
| Plaintext da insieme piccolo | Trudy prova tutti i possibili m con K_pub | **Soluzione: PKCS padding** |

**Dimensione raccomandata**: almeno 1024 bit per n (corrispondente a circa 150 cifre decimali).

### Attacchi attivi

| Attacco | Descrizione | Contromisura |
|---|---|---|
| Man-in-the-middle | Trudy sostituisce K_pub di Alice | PKI + certificati → [[pki]] |
| Timing attack (~1996) | Misura il tempo di decifratura/firma → rivela d | Aggiungere rumore casuale al tempo di esecuzione |
| Smooth numbers attack | Forgia firme su numeri smooth combinando firme note | PKCS: firma sempre su messaggi con padding random |

### Considerazione sul valore di e

**e = 65537** (2^16+1) è il valore raccomandato moderno:
- Piccolo → cifratura e verifica rapide
- Primo → facile trovare p,q t.c. (p-1)(q-1) siano coprimi con e
- Abbastanza grande da evitare problemi con e=3 senza PKCS

**Problema di e=3**: se m < ³√n, c = m³ (senza mod) → Trudy calcola ³√c. Anche con tre destinatari diversi (stessa m, diversi n₁n₂n₃) il Chinese Remainder Theorem permette di recuperare m³, quindi m. **Usare sempre PKCS.**

## Limitazioni pratiche

- RSA è usato solo su stringhe brevi (~200 bit) per due ragioni:
  1. **Computazionalmente inefficiente** rispetto al simmetrico
  2. **Modi operativi**: solo ECB o CBC (no CFB/OFB/CTR che richiedono solo E_k)
- Il flusso tipico in un protocollo reale è: RSA per scambiare una chiave AES effimera → AES per la comunicazione

## Articoli correlati

- [[crittografia-asimmetrica-fondamenti]]
- [[diffie-hellman]]
- [[pki]]
- [[protocolli-crittografici]]

## Fonti

- raw/2026-netsec-1d2-asymmetric-crypto-rsa.pdf
