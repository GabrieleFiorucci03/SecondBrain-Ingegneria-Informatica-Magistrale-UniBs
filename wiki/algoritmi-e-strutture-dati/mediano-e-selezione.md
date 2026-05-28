---
tags: [selezione, mediano, statistica-ordine, randomizzato, algoritmi]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Mediano e selezione_con_integrazioni.pdf
  - raw/selezione_addendum.pdf
---

# Mediano e Selezione

Il problema della **selezione** chiede: dato un insieme A di n numeri (distinti) e un indice i (1 ≤ i ≤ n), restituire l'elemento x ∈ A che è più grande di esattamente i–1 altri elementi — cioè la **i-esima statistica d'ordine** di A.

## Punti chiave

- **i-esima statistica d'ordine** = i-esimo elemento più piccolo dell'insieme
- **Mediano**: per n dispari è unico all'indice (n+1)/2; per n pari si preferisce l'indice ⌊n/2⌋
- Soluzione naive: ordinare con Merge-Sort/Heapsort → O(n·lg n), ma si può fare meglio
- **MINIMUM/MAXIMUM**: n–1 confronti; è il limite inferiore (analogo a torneo)
- **Min+Max simultanei**: 3⌈n/2⌉ confronti (analisi coppie) contro 2(n–1) separati
- **RANDOMIZED-SELECT**: caso medio **O(n)**, caso peggiore O(n²) (ma improbabile perché randomizzato)
- Esiste un algoritmo deterministico per la selezione in **O(n) nel caso peggiore** (non trattato in dettaglio)

## Selezione del minimo (e massimo)

```
MINIMUM(A)
1  min ← A[1]
2  for i ← 2 to length[A]
3    do if min > A[i]
4         then min ← A[i]
5  return min
```

Costo: n–1 confronti = **O(n)**. È il limite inferiore: ragionamento del torneo — ogni elemento eccetto il vincitore deve perdere almeno una partita → n–1 partite necessarie.

## Selezione simultanea di minimo e massimo

Calcolo indipendente: 2(n–1) confronti.

**Soluzione migliore** — analisi a coppie:
1. Prendere due elementi alla volta
2. Confrontarli tra loro (1 confronto)
3. Confrontare il minore con il minimo corrente (1 confronto)
4. Confrontare il maggiore con il massimo corrente (1 confronto)
→ 3 confronti per coppia = **3⌈n/2⌉ confronti totali**

Risparmio: ~n/2 confronti rispetto al calcolo indipendente.

## RANDOMIZED-SELECT

Selezione della i-esima statistica d'ordine in O(n) caso medio, usando RANDOMIZED-PARTITION (di Quicksort).

```
RANDOMIZED-SELECT(A, p, r, i)
1  if p = r then return A[p]
2  q ← RANDOMIZED-PARTITION(A, p, r)
3  k ← q – p + 1         ▷ k = n° elementi di A[p..q]
4  if i ≤ k
5    then return RANDOMIZED-SELECT(A, p, q, i)
6    else return RANDOMIZED-SELECT(A, q+1, r, i–k)
```

Precondizioni: elementi di A tutti distinti, p ≤ r, 1 ≤ i ≤ r–p+1. È una **ricorsione in coda** (tail recursion) → convertibile in versione iterativa.

## Analisi di RANDOMIZED-SELECT

**Caso peggiore**: T(n) = O(n²) — quando si sceglie sempre il perno min/max, riducendo il problema di una sola unità. Improbabile perché randomizzato.

**Caso medio**: T(n) = O(n). Ricorrenza:

T(n) ≤ (1/n) · [T(max(1,n–1)) + Σ_{k=⌈n/2⌉}^{n–1} 2T(k)] + O(n)

Semplificando: T(n) ≤ (2/n) · Σ_{k=⌈n/2⌉}^{n–1} T(k) + O(n)

**Dimostrazione** (metodo di sostituzione) — ipotesi T(n) ≤ c·n:

T(n) ≤ (2c/n) · Σ_{k=⌈n/2⌉}^{n–1} k + O(n)
     = (2c/n) · [n(n–1)/2 – (⌈n/2⌉–1)⌈n/2⌉/2] + O(n)
     ≤ c(3n/4 – 1/2) + O(n)
     ≤ cn  se c(n/4 + 1/2) ≥ O(n), cioè se c è sufficientemente grande.

**T(n) = O(n)**

## Articoli correlati

- [[quicksort]]
- [[algoritmi-di-ordinamento]]
- [[ordinamento-lineare]]

## Fonti

- `raw/Mediano e selezione_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
- `raw/selezione_addendum.pdf` — integrazioni manoscritte
