---
tags: [network-security, crittografia-simmetrica, AES, DES, block-cipher, stream-cipher, modi-operativi]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-1b-symmetric-crypto.pdf
---

# Crittografia Simmetrica

La **crittografia simmetrica** usa la **stessa chiave k** per cifrare e decifrare: E_k(m)=c, D_k(c)=m, D_k=E_k⁻¹. Comprende block cipher (stateless, blocchi fissi) e stream cipher (stateful, blocchi piccoli con memoria).

## Punti chiave

- **Goal fondamentale**: ogni bit di c deve dipendere da tutti i bit di k e di m; cambiare 1 bit di m deve causare il flip di ogni bit di c con probabilità 0.5 (**avalanche effect**).
- **Substitution cipher**: keyspace gigantesco (|K|=2^b!, l≈b·2^b) — inutilizzabile per b grande.
- **Transposition cipher**: keyspace piccolo (|K|=b!, l=log₂(b!)) ma preserva i bit → facile da attaccare.
- **Soluzione**: composizione (**confusion** + **diffusion**) in t round → product cipher.
- **DES** (ritirato 2005): Feistel, b=64, l=56; best attack = brute force su 2^55.
- **AES** (standard attuale): b=128, l=128/192/256; operazioni su GF(2^8); NSA classifica 128=SECRET, 256=TOP SECRET.
- I modi operativi offrono **solo confidenzialità** — per integrità e autenticazione usare sempre un **MAC**.

## Block cipher: principi costruttivi

### Substitution cipher
- Biiezione tra M e se stesso; k = indice a una permutazione di M
- |K| = (2^b)!, l = log₂(2^b!) ≈ b·2^b (per b=64, l ≈ 10²¹ bit → inusabile)
- Vulnerabile a frequency analysis se b piccolo

### Transposition cipher
- Permuta i bit del blocco; |K|=b!, l=log₂(b!) (per b=64, l≈384 bit — gestibile)
- Problema: preserva il numero di 0 e 1 → attaccabile

### Product cipher (Confusion + Diffusion)
- **Confusion** (substitution): nasconde la relazione tra k e c
- **Diffusion** (transposition/permutation): distribuisce l'influenza di ogni bit di m su tutti i bit di c
- Necessari **t > 1 round** per garantire che 1 bit di m influenzi tutti i bit di c con p=0.5

## DES — Data Encryption Standard

**Caratteristiche:** b=64, l=56 (64-8 bit di parità), Feistel, 1975 (IBM/NSA), standardizzato NIST 1977, **ritirato 2005**.

**Struttura** (Feistel type):
```
64-bit input → IP → 16 round → 32-bit swap → IP⁻¹ → 64-bit output
```

**Un singolo round i** (Feistel):
```
L_{i+1} = R_i
R_{i+1} = L_i ⊕ f(R_i, k_i)
```

**Funzione interna f** (32-bit R_i → 32-bit output):
1. Expansion: 32→48 bit (un bit di R_i entra in due S-Box → avalanche effect)
2. XOR con k_i (48 bit)
3. 8 S-Box: 6 bit → 4 bit (unica parte **non-lineare** di DES — tutta la sicurezza è qui)
4. Permutazione P: 32→32 bit (distribuzione ai round successivi)

**Generazione sottochiavi:** 64-bit k → scarta 8 bit parità → 56 bit → P0 → C₀D₀ (28+28) → 16 round con shift e P1 (compression) → 16 × k_i da 48 bit.

**Decryption:** D_k = E_k con sottochiavi in ordine inverso.

**Debolezze:**
- Complementation property: keyspace effettivo per brute-force = 2^55
- 4 chiavi deboli (E_j = D_j) e 12 semi-deboli (E_j = D_k)
- IP e IP⁻¹: non aumentano la sicurezza (probabilmente ostacolano implementazioni SW)
- S-Box: parametri di design mai pubblicati; ottimizzate contro differential cryptanalysis (noto solo negli anni '90!)

**Pro:** molto criptanalizzato, nessuna falla grave trovata; best attack = brute force.

**3DES (TDEA):** E_k1 → D_k2 → E_k1, k=k1|k2=112 bit, |K|=2^112. Compatibile con DES (k1=k2). Deprecato 2019, approvato NIST fino al 2030. La struttura EDE difende dal meet-in-the-middle attack (che renderebbe 2DES sicuro solo 2^57).

## IDEA e Blowfish

| Cipher | Anno | b | l | Note |
|---|---|---|---|---|
| IDEA | 1991 | 64 | 128 | Patentato; usato in PGP, S/MIME; resiste a differential cryptanalysis |
| Blowfish | 1994 | 64 | 32–448 | Royalty-free; ottimo in SW; usato in OpenSSH, PGPfone |

## AES — Advanced Encryption Standard

**Caratteristiche:** b=128, l=128/192/256 (AES-128/192/256), Rijndael (Daemen+Rijmen), standardizzato NIST 2001, royalty-free.

**Struttura matematica:** GF(2^8) — ogni byte è un polinomio di grado ≤7 con coefficienti in {0,1}; somma = XOR, prodotto = moltiplicazione polinomiale mod p(x)=x^8+x^4+x^3+x+1.

**Stato:** matrice 4×4 byte (4×4×8=128 bit).

**Quattro operazioni per round:**

| Operazione | Tipo | Scopo |
|---|---|---|
| **SubBytes** | Non-lineare | Confusion: ogni byte → S-box (LUT basata su inverso moltiplicativo) |
| **ShiftRows** | Lineare | Rotazione di righe (evita che AES cifri colonne separatamente) |
| **MixColumns** | Lineare | Diffusion: ogni colonna moltiplicata per C(x) = [[2,3,1,1],[1,2,3,1],[1,1,2,3],[3,1,1,2]] |
| **AddRoundKey** | Lineare | XOR dello stato con la sottochiave del round |

**Round:**
- Round iniziale: solo AddRoundKey
- 9/11/13 round principali: SubBytes → ShiftRows → MixColumns → AddRoundKey
- Round finale: SubBytes → ShiftRows → AddRoundKey (no MixColumns)

**Sottochiavi:** key schedule genera 11/13/15 sottochiavi da 128 bit.

**Vulnerabilità:**
- Brute force su AES-128: ≈ 2^126.5 (leggermente migliorato)
- Side-channel (es. cache-based attacks su OpenSSL)
- Quantum: AES-128/192 vulnerabili, AES-256 considerato sicuro (post-quantum)

**NSA:** 128 bit = livello SECRET; 256 bit = livello TOP SECRET.

**Performance SW (OpenSSL 2022):** AES-128 ≈ 17–46 Gb/s (con AES-NI); 3DES ≈ 172–821 Mb/s.

## Stream cipher

I block cipher sono **stateless**; gli stream cipher sono **stateful**: l'output dipende da m_i, k e lo stato interno S_i.

### Synchronous stream cipher
```
c_i = h(m_i, z_i)    (solitamente: c_i = m_i ⊕ z_i)
z_i = g(s_i, K)      (keystream)
s_{i+1} = f(s_i, K)
```
- Pro: errori non propagano
- Contro: perdere un blocco rompe tutta la decifratura successiva (richiede sincronizzazione)
- Non adatto a random access

### Self-synchronizing stream cipher
```
z_i = g(c_{i-1},...,c_{i-t}, K)    (keystream da ciphertext precedente)
```
- Usa un **Initialization Vector (IV)** trasmesso in chiaro
- Self-synchronizing: errori su c_i corrompono solo i blocchi corrispondenti + al massimo t seguenti

### RC4
- Synchronous, Rivest 1987, leaked 1994; h=h⁻¹=XOR; chiave 1–256 byte
- **Scartare sempre i primi 256 byte** (alta correlazione con la chiave)
- Decommissionato IETF 2015 (RFC 7465, TLS)

### ChaCha-20
- Derivato da Salsa-20, pubblicato 2008; XOR + somme + rotazioni; 16 interi 32-bit (matrice 4×4)
- k=256 bit, nonce=128 bit; best attack: 2^236
- Più veloce di AES su piattaforme senza AES-NI; non aumenta la dimensione del ciphertext

## Modi operativi per block cipher

Regole per usare un block cipher con plaintext più lunghi di b bit. **Tutti offrono solo confidenzialità** — usare sempre un MAC per integrità.

| Modo | Formula | Stateful | Error prop. | Note |
|---|---|---|---|---|
| **ECB** | c_i=E_k(m_i) | No | No | **Mai usare** — vulnerabile a frequency analysis e block reorder |
| **CBC** | c_i=E_k(m_i⊕c_{i-1}), c_0=IV | Sì | 1 blocco | IV deve essere fresh; resistente a freq. analysis |
| **CFB** | c_i=m_i⊕E_k(c_{i-1}), c_0=IV | Sì | ≈ t blocchi | Self-synchronizing; usato in OpenPGP; solo E_k (no D_k) |
| **OFB** | c_i=o_i⊕m_i, o_i=E_k(o_{i-1}), o_0=IV | Sì | No | Synchronous stream cipher; keystream precomputabile; IV unico per (IV,k) |
| **CTR** | c_i=E_k(IV+i-1)⊕m_i | No | No | Random-access; parallelizzabile; solo E_k |

**Note importanti:**
- **ECB**: blocchi identici → cifrature identiche → **Never use ECB!**
- **CBC**: IV deve essere fresh se si riusa la stessa chiave su più messaggi
- **OFB/CTR**: riusare (IV,k) è catastrofico — XOR dei due ciphertext rivela XOR dei plaintext
- **CFB, OFB, CTR**: non utilizzabili con cifrari asimmetrici (richiedono solo E_k o solo D_k)
- **OCB** (non trattato nel corso): unico modo che fornisce simultaneamente confidenzialità e integrità

## Articoli correlati

- [[introduzione-crittografia]]
- [[funzioni-hash]]
- [[crittografia-asimmetrica-fondamenti]]
- [[protocolli-crittografici]]

## Fonti

- raw/2026-netsec-1b-symmetric-crypto.pdf
