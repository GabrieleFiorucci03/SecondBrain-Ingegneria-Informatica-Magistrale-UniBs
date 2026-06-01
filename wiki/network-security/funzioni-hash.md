---
tags: [network-security, hash, MAC, HMAC, MDC, integrità, autenticazione, SHA, MD5]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-1c-hash.pdf
---

# Funzioni Hash e MAC

Le **funzioni hash** (o *message digest functions*) mappano una stringa di lunghezza arbitraria in una stringa di lunghezza fissa n bit — il **digest** d=h(m). Sono lo strumento fondamentale per integrità e autenticazione dei messaggi.

## Punti chiave

- Le funzioni hash si dividono in **MDC** (senza chiave, integrità) e **MAC** (con chiave, integrità + autenticazione).
- Proprietà fondamentali: preimage resistance (one-way), weak collision resistance, strong collision resistance.
- **Birthday attack**: trovare una collisione richiede O(2^n/2) operazioni — hash di almeno 256 bit per sicurezza moderna.
- **Mai usare MD5 o SHA-1**: entrambi sono rotti (collisioni trovate, MD5 in 8h su P4/1.6GHz).
- **Usare SHA-256/512 o SHA-3** per applicazioni attuali.
- L'esistenza di OWHF senza ipotesi equivale a dimostrare P≠NP.

## Proprietà desiderate

| # | Proprietà | Definizione formale | Nome alternativo |
|---|---|---|---|
| 1 | Efficienza | dato m, calcolare d=h(m) è facile | — |
| 2 | Uniform distribution | P[d=h(m)]=2⁻ⁿ; cambiare 1 bit di m cambia ogni bit di d con p=0.5 | Avalanche effect |
| 3 | **Preimage resistance** | dato d, trovare m tale che h(m)=d è computazionalmente infeasibile | One-way property |
| 4 | **Weak collision resistance** | dato m₁ e d₁=h(m₁), trovare m₂≠m₁ con h(m₂)=d₁ è infeasibile | 2nd preimage resistance |
| 5 | **Strong collision resistance** | trovare qualsiasi coppia (m₁,m₂) con m₁≠m₂ e h(m₁)=h(m₂) è infeasibile | Collision resistance |

**Birthday problem**: con |D|=2ⁿ digest, per avere probabilità 0.5 di trovare una collisione bastano ~2^(n/2) messaggi. Conseguenza: **n deve essere almeno 256 bit** per resistere al birthday attack con sicurezza ≥128 bit.

**Brute-force per preimage/2nd preimage**: O(2ⁿ) operazioni.

## Perché queste proprietà sono importanti

- **Preimage resistance** → challenge-response authentication (d=h(challenge, key))
- **Weak collision resistance** → firme digitali (si firma il digest d invece del messaggio m intero)
- **Strong collision resistance** → firme digitali quando l'attaccante ha margine di manovra su m; più stringente delle due precedenti

## MDC vs MAC

### MDC — Modification Detection Code (hash senza chiave)
- d = h(m), nessuna chiave condivisa
- **Goal**: integrare la protezione di m proteggendo d con un canale sicuro o firma digitale
- Un MDC è sicuro se offre preimage resistance + strong collision resistance
- **Attacchi possibili**: trovare m' tale che h(m')=d (preimage), o trovare una collisione h(m)=h(m')

### MAC — Message Authentication Code (hash con chiave)
- d = h_k(m), Alice e Bob condividono chiave k
- Alice invia (m, MAC); Bob calcola MAC'=h_k(m') e verifica MAC'=MAC
- Garantisce: integrità di m + autenticazione del mittente (ma non firma/non-ripudio)
- **Attacchi possibili**: recuperare k da abbastanza coppie (m_i, d_i); calcolare d'=h_k(m') senza conoscere k; trovare collisione con chiave

## Costruzione iterativa (Merkle-Damgård)

Struttura base per MDC:
```
m (qualsiasi lunghezza)
  → Append lunghezza originale
  → Padding a multiplo di q bit
  → t blocchi x₁,...,xₜ (q bit ciascuno)
  → r_i = f(x_i, r_{i-1}),  r₀ = IV
  → Final transform g (opzionale)
  → d (n bit)
```

**Metodo Matyas-Meyer-Oseas**: costruisce f da un block cipher E_k:
```
r_i = x_i ⊕ E_k(x_i),   k = w(r_{i-1})
```
Lo XOR con x_i previene meet-in-the-middle attacks. Problema: la lunghezza dell'hash dipende dal block size del cifrario (solitamente troppo piccolo).

## MD5

**Caratteristiche:** RFC 1321, 1991 (successor MD4), n=128, q=512, 4-pass compression.

**Padding:** 1-bit + zeri + lunghezza originale mod 2^64 (in 64 bit) → multiplo di 512 bit.

**Status: ROTTO.** Dal 1996 dubbi sulla sicurezza; nel 2005 dimostrato che 8h su P4/1.6GHz bastano per trovare collisioni. Dimostrati anche collision per file immagine con lo stesso digest. **Mai usare MD5.**

## SHA-1

**Caratteristiche:** RFC 3174, 1993, n=160, q=512, 5-pass compression, m < 2^64 bit.

**SHAttered attack (Google, 2017):** prima collisione pratica trovata con O(2^69) operazioni (ideale sarebbe 2^80). Richiesto: 9×10^18 SHA-1 computations, 6500 anni CPU + 110 anni GPU.

**Status: ROTTO.** **Mai usare SHA-1.**

## SHA-256/384/512 e SHA-3

| Algoritmo | n (bit) | q (bit) | Status |
|---|---|---|---|
| SHA-256 | 256 | 512 | Sicuro, raccomandato |
| SHA-384 | 384 | 1024 | Sicuro |
| SHA-512 | 512 | 1024 | Sicuro |
| SHA-3 | variabile | — | Design completamente nuovo (sponge construction), future-proof, efficiente in HW |

**SHA-3** (Keccak) ha un design radicalmente diverso da SHA-1/2 (non Merkle-Damgård) → migliore resistenza futura anche se SHA-2 fosse compromesso.

**Performance (OpenSSL):** MD5 ≈ 2.2–4.3 Gb/s; SHA-1 ≈ 6.7–17 Gb/s; SHA-256 ≈ 4.4–11.5 Gb/s.

## Raccomandazione pratica

```
Mai usare: MD5, SHA-1
Usare: SHA-256/512 (minimo), SHA-3 (future-proof)
```

## Costruire un MAC da un MDC: approcci naïve e HMAC

Costruire un MAC sicuro da un MDC è più difficile di quanto sembri.

**Approcci naïve che falliscono:**

| Approccio | Formula | Vulnerabilità |
|---|---|---|
| Suffix-only | g(m\|k) | Birthday attack offline: trovare collisione su g() permette di attaccare MAC_k senza conoscere k |
| Prefix-only | g(k\|m) | Length extension: da d=MAC_k(m) si può derivare d'=MAC_k(m\|x) senza conoscere k, usando d come r_i iniziale |
| MD5-envelope (RFC 1828) | MD5(k\|y\|m\|k) | Sotto certe ipotesi, osservare abbastanza coppie (d_i,m_i) può rivelare k |

### HMAC (RFC 2104)

Il MAC corretto da usare. Formula (Bellare, Canetti, Krawczyk):

```
HMAC-MDC_k(m) = MDC( (k|0...) ⊕ c₁  |  MDC( ((k|0...) ⊕ c₂) | m ) )
```

- k|0... = k paddato a q bit (512 bit per MD5/SHA-1)
- c₁, c₂ = costanti pubbliche (ipad e opad)
- Due livelli di MDC: inner hash protegge m, outer hash include k

**Sicurezza:** il miglior attacco è il birthday attack. Con HMAC-MD5 bisogna osservare ≥2^64 blocchi da 512 bit generati con la stessa chiave (≈2^73 bit ≈ 25000 anni su link da 10 Gb/s). **HMAC è the MAC to use.**

## Articoli correlati

- [[introduzione-crittografia]]
- [[crittografia-simmetrica]]
- [[protocolli-crittografici]]
- [[crittografia-asimmetrica-fondamenti]]

## Fonti

- raw/2026-netsec-1c-hash.pdf
