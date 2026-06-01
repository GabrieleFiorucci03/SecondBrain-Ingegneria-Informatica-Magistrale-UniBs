---
tags: [network-security, Diffie-Hellman, crittografia-asimmetrica, logaritmo-discreto, key-exchange, DSS, ECC]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-1d3-asymmetric-crypto-dh.pdf
---

# Diffie-Hellman e Key Exchange Asimmetrico

Il protocollo **Diffie-Hellman** (1976, Diffie & Hellman) è il **primo meccanismo asimmetrico mai reso pubblico**. Obiettivo: stabilire una chiave di sessione temporanea K tra due parti **senza credenziali pre-esistenti**, su un canale insicuro. Sicurezza basata sulla difficoltà del **logaritmo discreto**.

*Nota: Williamson del CESG britannico inventò lo stesso meccanismo prima del '76, ma rimase classificato.*

## Punti chiave

- **Problema base**: b = aⁱ mod p è facile da calcolare; trovare i dato a, b, p è computazionalmente infeasibile (logaritmo discreto).
- DH stabilisce una chiave K **non autenticata** → vulnerabile a Man-in-the-Middle; deve sempre essere usato con meccanismi di autenticazione.
- **Secure prime**: p è un secure prime se (p-1)/2 è anch'esso primo (p=2q+1, con q primo) → protocollo più robusto.
- **DSS** (NIST 1991): standard di firma digitale basato su ElGamal/log discreto; patent-free; progettato per essere più leggero nel signing che nel verifying (smart-card).
- **ECC**: stessa sicurezza di RSA/DH con chiavi ~10x più corte; basato su problemi senza soluzioni sub-esponenziali note.
- I cifrari asimmetrici (RSA) non possono usare i modi OFB/CFB/CTR (richiedono E_k sia per E che per D); usare CBC o OCB.

## Il protocollo DH

**Parametri pubblici** (condivisi e noti a tutti, inclusa Trudy):
- p: numero primo grande
- g: generatore di Z_p* (∀k: 1≤k≤p-1, g^k mod p percorre tutti gli elementi di Z_p*)

**Scambio:**
```
Alice:
  1. Sceglie Sa casuale, 1 ≤ Sa < p
  2. Calcola Ta = g^Sa mod p
  3. Invia Ta a Bob (in chiaro)

Bob:
  1. Sceglie Sb casuale, 1 ≤ Sb < p
  2. Calcola Tb = g^Sb mod p
  3. Invia Tb ad Alice (in chiaro)

Derivazione chiave condivisa:
  Alice: K = Tb^Sa mod p = (g^Sb)^Sa mod p = g^(SbSa) mod p
  Bob:   K = Ta^Sb mod p = (g^Sa)^Sb mod p = g^(SaSb) mod p
  → K = g^(Sa·Sb) mod p  (uguale per entrambi!)
```

**Perché funziona**: Trudy osserva p, g, Ta, Tb ma non può recuperare Sa o Sb (logaritmo discreto), né calcolare K direttamente da Ta e Tb (Diffie-Hellman problem ← almeno altrettanto difficile del log discreto).

## Sicurezza e attacchi

### Attacchi passivi
- Il miglior algoritmo per il log discreto è sub-esponenziale, super-polinomiale (stessa classe di fattorizzazione)
- **Secure prime**: p sicuro se (p-1)/2 è primo → p=2q+1. Evita alcune debolezze matematiche
- Scegliere p e g è altrettanto complesso che scegliere n in RSA

### Attacchi attivi: Man-in-the-Middle
DH non autentica i partecipanti → Trudy può intercettare e sostituire Ta e Tb con i propri valori:
```
Alice ←→ Trudy ←→ Bob
```
Trudy stabilisce K₁ con Alice e K₂ con Bob → legge e re-cifra tutto.

**Soluzioni parziali:**
- Usare sempre nuovi (p,g) e (Ta,Tb) per ogni sessione — più sicuro ma Trudy può sempre fare MitM
- Usare gli stessi (p,g) fissi: Trudy può precalcolare tabelle di log discreti mod p → compromette tutte le sessioni future con quel p

**Soluzione vera**: accoppiare DH con un meccanismo di autenticazione (es. PKI + certificati, o RSA firma). Vedere [[protocolli-crittografici]] e [[tls]].

## DSS — Digital Signature Standard

- NIST, 1991; basato su **ElGamal** (logaritmo discreto); patent-free
- Progettato per essere **leggero nel signing, pesante nel verifying** → adatto a smart-card
- Controversia: proposto da NIST, "approvato" da NSA (similar al caso DES). Perché ElGamal e non RSA? (RSA era brevettato all'epoca)
- Debolezza storica: componenti di 512 bit dimostratamente falsificabili con 25M$ di hardware
- Patent RSA scaduto settembre 2000 → DSS ha perso il suo vantaggio principale

## ECC — Elliptic Curve Cryptography

ECC generalizza RSA/DH su curve ellittiche in campi finiti:
- **Problema base**: logaritmo discreto su curve ellittiche — non ha soluzioni sub-esponenziali note (a differenza di RSA/DH)
- **Vantaggio**: stessa sicurezza di RSA-1024 con chiavi di ~100 bit → **10x più corte**
- **Impatto pratico**: fondamentale per dispositivi mobili e IoT (risorse limitate)
- Non coperto nel corso, ma largamente usato (TLS, SSH, Bitcoin, etc.)

## Articoli correlati

- [[crittografia-asimmetrica-fondamenti]]
- [[rsa]]
- [[pki]]
- [[protocolli-crittografici]]
- [[tls]]

## Fonti

- raw/2026-netsec-1d3-asymmetric-crypto-dh.pdf
