---
tags: [network-security, crittografia, crittologia, entropia, sicurezza]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-1a-crypto-intro.pdf
---

# Introduzione alla Crittografia

La **crittologia** è l'area di ricerca che studia la segretezza delle comunicazioni: come permettere ad Alice e Bob di comunicare su un canale pubblico senza che Trudy possa intercettare, alterare o impersonare i partecipanti.

Crittologia = **crittografia** (algoritmi matematici di trasformazione) + **protocolli** (regole d'uso) + **crittoanalisi** (studio degli attacchi).

## Punti chiave

- La sicurezza di un sistema crittografico deve dipendere **esclusivamente dalla chiave** k, non dalla segretezza dell'algoritmo (principio di Kerckhoffs).
- Un algoritmo è **breakable** se un crittoanalista può recuperare m senza conoscere k₂, dato tempo sufficiente — la breakability è relativa a tempo e potenza computazionale.
- L'obiettivo del crittografo è che il miglior attacco possibile sia la **ricerca esaustiva della chiave**.
- La **perfect secrecy** (Shannon) richiede che c e m siano statisticamente indipendenti: osservare c non fornisce alcuna informazione su m.
- Condizione necessaria per perfect secrecy: H(k) ≥ H(m) — l'entropia della chiave deve essere almeno pari a quella del messaggio.
- La crittanalisi non è sempre l'attacco più efficace: spesso è più facile colpire l'implementazione o l'utente (social engineering, physical coercion).

## Notazione

| Simbolo | Significato |
|---|---|
| A | Alfabeto: insieme finito di simboli (solitamente binario) |
| M, m | Insieme dei plaintext; singolo plaintext |
| C, c | Insieme dei ciphertext; singolo ciphertext |
| K, k=(k₁,k₂) | Insieme delle chiavi; keypair (cifratura, decifratura) |
| \|X\| | Cardinalità di X |
| \| | Operatore di concatenazione |

Lo schema generale: E_k1(m) = c (cifratura), D_k2(c) = m (decifratura), con D_k2 = E_k1⁻¹.
E_k1 è una **biiezione** tra M e C.

## Tipi di algoritmi crittografici

### Block cipher
- m è una stringa di **b bit** (block size); anche c è b bit
- E_k1 è **memory-less**: lo stesso m con la stessa k₁ produce sempre lo stesso c
- Messaggi più lunghi di b bit vengono frammentati in blocchi m_i
- Idealmente, alterare 1 bit di m cambia ~50% dei bit di c (**error propagation**)
- Sicurezza proporzionale a b: blocchi piccoli sono vulnerabili alla frequency analysis
- Un block cipher perfettamente casuale implementa una delle 2^b! permutazioni degli elementi di M

### Stream cipher
- Caso speciale di block cipher con block size piccolo (1–8 bit)
- E e D mantengono uno **stato** S_i che dipende da plaintext, ciphertext, tempo, ecc.
- Lo stesso m_i in posizioni diverse produce c_i diversi → no pattern ripetuti
- Meno intensi computazionalmente; adatti a dispositivi a bassa potenza/memoria
- Configurabili per non propagare errori di trasmissione

## Crittoanalisi

**Kerckhoffs' principle**: l'attaccante (Trudy) conosce E, D, C, M e c — la sicurezza deve dipendere solo da k.

**Tipi di attacco** (in ordine crescente di potere dell'attaccante):

| Tipo | Cosa conosce l'attaccante |
|---|---|
| Ciphertext-only | Solo c (es. frequency analysis). Un algoritmo vulnerabile a questo è definitivamente insicuro. |
| Known plaintext | Alcune coppie (c, m). Obiettivo: recuperare k. |
| Chosen plaintext | Può scegliere m e ottenere il c corrispondente. |

Un buon algoritmo deve resistere a tutti e tre.

## Teoria dell'informazione e crittografia

**Entropia** di una variabile casuale discreta x con |X| = q:
```
H(x) = Σᵢ pᵢ · log₂(1/pᵢ)
```
- H(x) = 0 ⟺ esito certo
- H(x) = log₂q ⟺ distribuzione uniforme (massima incertezza)

**Applicazione alla crittografia:**
- H(m): incertezza sul messaggio trasmesso
- H(k): se k è una stringa random di l bit → H(k) = l
- Obiettivo ideale: H(c) ≈ log₂|C|, indipendentemente da m

**Perfect secrecy** (Shannon, 1949): un algoritmo offre perfect secrecy (è *unconditionally secure*) se e solo se c e m sono **statisticamente indipendenti**. Condizione necessaria: **H(k) ≥ H(m)**.

## Articoli correlati

- [[crittografia-simmetrica]]
- [[crittografia-asimmetrica-fondamenti]]
- [[funzioni-hash]]
- [[protocolli-crittografici]]

## Fonti

- raw/2026-netsec-1a-crypto-intro.pdf
