---
tags: [network-security, crittografia-asimmetrica, teoria-dei-numeri, RSA, Diffie-Hellman, modulare]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/2026-netsec-1d1-asymmetric-crypto-primer.pdf
---

# Crittografia Asimmetrica — Fondamenti

La **crittografia asimmetrica** usa una **coppia di chiavi** (k₁=pubblica, k₂=privata): chiunque può cifrare con k₁, solo il possessore di k₂ può decifrare. La sicurezza si basa sulla difficoltà computazionale di invertire certi problemi matematici — ad oggi nessuno ha dimostrato formalmente questa sicurezza, ma nessuno ha trovato soluzioni efficienti.

## Punti chiave

- **Principio**: operazioni facili in un senso, computazionalmente infeasibili al contrario (es. n=p·q facile; fattorizzare n conoscendo solo n, difficile).
- Proprietà aggiuntiva rispetto al simmetrico: **derivare k₂ da k₁ deve essere impossibile**.
- Asimmetrico è **ordini di grandezza più lento** del simmetrico → in pratica si usa asimmetrico per scambiare una chiave simmetrica effimera.
- **ECC** (Elliptic Curve Cryptography, non trattata nel corso): alternativa più efficiente, basata su logaritmi in campi finiti.
- Tassonomia: **DH/ElGamal** (log discreto), **RSA** (fattorizzazione), **DSS** (log discreto).
- **MAC** vs **firma digitale**: il MAC richiede chiave condivisa (nessuna non-ripudio); la firma usa chiave privata → non-ripudio + autenticazione verificabile da chiunque.

## Tassonomia degli algoritmi asimmetrici

| Algoritmo | Problema base | Usi |
|---|---|---|
| Diffie-Hellman | Logaritmo discreto | Key derivation |
| RSA | Fattorizzazione intera | Confidenzialità, firma, key derivation |
| ElGamal | Logaritmo discreto | Firma |
| DSS | Logaritmo discreto | Firma |

**Usi principali:**
- **Confidenzialità**: E_k1(m)=c; D_k2(c)=m → solo il possessore di k₂ può leggere
- **Firma digitale**: E_k2(m)=firma; D_k1(firma)=m → tutti possono verificare, solo chi ha k₂ può firmare → authentication + integrity + non-repudiation
- **Key derivation**: accordo su chiave effimera k (DH) → poi uso simmetrico

## Teoria dei numeri (primer per RSA/DH)

### Definizioni di base

- **Numero primo**: divisibile solo da se stesso e 1
- **MCD** (Greatest Common Divisor): gcd(x,y) = il più grande intero positivo che divide entrambi; gcd(0,x)=x
- **Coprimi** (relatively prime): x e y sono coprimi ⟺ gcd(x,y)=1

### Aritmetica modulare

**Operatore mod n**: x mod n = resto della divisione di x per n (il più piccolo non-negativo r t.c. x=kn+r)

**Relazione di congruenza**: a ≡ b (mod n) ⟺ ∃k: a-b=kn (stesso resto diviso n)

**Proprietà** (si comporta come aritmetica ordinaria):
```
[(a modn) + (b modn)] modn = (a+b) modn
[(a modn) · (b modn)] modn = (a·b) modn
```

**Inverso moltiplicativo mod n**: x⁻¹ mod n esiste ⟺ gcd(x,n)=1. Si calcola con l'**algoritmo euclideo esteso**.

### Insiemi Z_n e Z_n*

- **Z_n** = {0, 1, ..., n-1} — tutti i numeri mod n
- **Z_n*** = {x ∈ Z_n : gcd(x,n)=1} — numeri mod n coprimi con n
  - Se n è primo: Z_n* = Z_n \ {0} (tutti tranne 0)
  - Es.: Z_8* = {1,3,5,7}; Z_11* = {1,2,...,10}

**(Z_n*, ·) è un gruppo abeliano**: chiusura, associatività, elemento identità (1), inverso per ogni elemento, commutatività.

### Algoritmo euclideo (esteso)

**gcd(x,y)**: applica ricorsivamente gcd(x,y) = gcd(x, y mod x) fino a gcd(x,0)=x.

**Algoritmo euclideo esteso**: calcola gcd(x,y) **e** x⁻¹ mod y (e y⁻¹ mod x), se esistono. Ad ogni iterazione n mantiene r_n = u_n·x + v_n·y; se al termine r_n=1 allora u_n = x⁻¹ mod y.

### Teorema cinese del resto (CRT)

Sia N = ∏ᵢ nᵢ con gcd(nᵢ,nⱼ)=1 per i≠j. La corrispondenza X ↔ {xᵢ = X mod nᵢ} è una **biiezione** tra Z_N e Z_n₁ × Z_n₂ × ... × Z_nₖ.

**Utilità**: decompone operazioni su grandi numeri X mod N in operazioni su piccoli numeri xᵢ mod nᵢ — usato per accelerare RSA.

**Ricostruzione**: X = (Σᵢ xᵢ·Nᵢ·yᵢ) mod N,  dove Nᵢ=N/nᵢ, yᵢ=Nᵢ⁻¹ mod nᵢ.

### Funzione totiente di Eulero φ(n)

φ(n) := |Z_n*| (numero di interi in [1,n-1] coprimi con n)

**Teoremi:**
- Se n è primo: φ(n) = n-1
- Se n = p·q con p,q primi: **φ(n) = (p-1)·(q-1)** ← fondamentale per RSA

### Teorema di Eulero

**TH**: per ogni x ∈ Z_n*, **x^φ(n) ≡ 1 (mod n)**

**Corollario**: per ogni x ∈ Z_n*, per ogni k≥0: x^(kφ(n)+1) ≡ x (mod n)

**Formulazione equivalente**: x^(1 mod φ(n)) ≡ x (mod n)

**Generalizzazione** (fondamentale per RSA): se n=n₁·n₂ con n₁,n₂ primi, allora per ogni x ∈ Z_n e per ogni k≥0:
```
x^(kφ(n)+1) ≡ x (mod n)
```
(vale anche per x ∉ Z_n*, non solo per i coprimi con n)

### RSA spoiler (dal teorema di Eulero)

Il teorema di Eulero **spiega esattamente perché RSA funziona**:
```
1. Scegli n₁, n₂ primi → n = n₁·n₂
2. Calcola φ(n) = (n₁-1)·(n₂-1)
3. Scegli e coprimo con φ(n)
4. Calcola d = e⁻¹ mod φ(n)  (esiste perché gcd(e,φ(n))=1)
5. Cifra: c = m^e mod n
6. Decifra: c^d mod n = m^(ed) mod n = m^(1 mod φ(n)) mod n = m  ✓
```

## Articoli correlati

- [[rsa]]
- [[diffie-hellman]]
- [[introduzione-crittografia]]
- [[protocolli-crittografici]]

## Fonti

- raw/2026-netsec-1d1-asymmetric-crypto-primer.pdf
