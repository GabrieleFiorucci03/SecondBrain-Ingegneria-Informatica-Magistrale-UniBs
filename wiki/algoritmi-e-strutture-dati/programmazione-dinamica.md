---
tags: [programmazione-dinamica, ottimizzazione, bottom-up, memoization, prodotto-matrici, LCS]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Programmazione_dinamica_con_integrazioni.pdf
  - raw/programmazione_dinamica_addendum.pdf
  - raw/prodotto_matriciale.pdf
  - raw/ricorsione_in_coda.pdf
---

# Programmazione Dinamica

Paradigma per la sintesi di algoritmi che risolvono problemi di **ottimizzazione** combinando soluzioni di sottoproblemi. A differenza di Divide-et-Impera (sottoproblemi indipendenti, risolti ogni volta), la PD risolve ogni sottoproblema **una sola volta**, memorizza la soluzione in una tabella e la riusa quando si ripresenta lo stesso sottoproblema.

## Punti chiave

- Applicabile quando il problema ha **sottostruttura ottima**: ogni soluzione ottima contiene soluzioni ottime dei sottoproblemi
- Applicabile quando il problema ha **sottoproblemi sovrapposti**: la ricorsione ingenua risolve lo stesso sottoproblema più volte
- **4 fasi**: (1) caratterizzazione struttura soluzione ottima, (2) definizione ricorsiva del valore ottimo, (3) calcolo bottom-up, (4) ricostruzione soluzione (opzionale)
- **Bottom-up** (tabulazione): calcola soluzioni dai sottoproblemi più piccoli ai più grandi
- **Memoizzazione** (top-down): ricorsione + cache — equivalente in complessità al bottom-up
- Differenza da Divide-et-Impera: la PD si applica anche a sottoproblemi **non indipendenti**

## PD vs Divide-et-Impera

| | Divide-et-Impera | Programmazione Dinamica |
|---|---|---|
| Sottoproblemi | Indipendenti | Possono sovrapporsi |
| Soluzione sottoproblema | Ricalcolata ogni volta | Memorizzata e riusata |
| Approccio | Top-down ricorsivo | Bottom-up iterativo (o top-down con memo) |
| Esempi | Merge-Sort, Quicksort | Matrix-Chain-Order, LCS |

## I due criteri di applicabilità

**1. Sottostruttura ottima**: una soluzione ottima del problema contiene al suo interno soluzioni ottime dei sottoproblemi.

**2. Sottoproblemi sovrapposti**: l'albero di ricorsione ingenuo contiene più nodi con lo stesso sottoproblema. Se i sottoproblemi fossero tutti distinti, la PD non darebbe vantaggi rispetto a D&I.

## Esempio: prodotto ottimo di una sequenza di matrici

**Problema**: data ⟨A₁, …, Aₙ⟩ con Aᵢ di dimensioni pᵢ₋₁×pᵢ, trovare la parentesizzazione completa del prodotto che minimizza il n° di moltiplicazioni scalari.

**Perché non brute-force**: P(n) = numeri di Catalani = C(n–1) = Ω(4ⁿ/n^{3/2}) parentesizzazioni.

**Prodotto di due matrici** Aₚ,q · Bq,r: costo = p·q·r moltiplicazioni scalari.

### Fase 1: sottostruttura ottima

Nella parentesizzazione ottima di A₁..ₙ esiste un k (1 ≤ k < n) tale che:
- costo(A₁..ₙ) = costo(A₁..k) + costo(Aₖ₊₁..ₙ) + p₀·pₖ·pₙ
- Le parentesizzazioni di A₁..k e Aₖ₊₁..ₙ devono essere a loro volta ottime

### Fase 2: ricorrenza

m[i,j] = n° minimo di moltiplicazioni scalari per calcolare Aᵢ..ⱼ

```
m[i,j] = 0                                          se i = j
m[i,j] = min_{i≤k<j} { m[i,k] + m[k+1,j] + pᵢ₋₁·pₖ·pⱼ }   se i < j
```

s[i,j] = k che minimizza m[i,j] (per ricostruire la parentesizzazione ottima).

### Fase 3: calcolo bottom-up — MATRIX-CHAIN-ORDER

```
MATRIX-CHAIN-ORDER(p)          → T(n) = Θ(n³), spazio Θ(n²)
1  n ← length[p] – 1
2  for i ← 1 to n do m[i,i] ← 0      ▷ sottoproblemi di lunghezza 1
4  for l ← 2 to n                      ▷ l = lunghezza sequenza
5    do for i ← 1 to n–l+1
6         do j ← i + l – 1
7            m[i,j] ← ∞
9            for k ← i to j–1
10               q ← m[i,k] + m[k+1,j] + p[i–1]·p[k]·p[j]
11               if q < m[i,j] then m[i,j] ← q; s[i,j] ← k
14 return m, s
```

**Analisi**: Θ(n²) sottoproblemi distinti, ciascuno richiede Θ(n) → **Θ(n³)** totale.

**Esempio** (p = ⟨30,35,15,5,10,20,25⟩, n=6):
- Soluzione ottima: m[1,6] = 15125 con s[1,6]=3
- Parentesizzazione: ((A₁(A₂A₃))((A₄A₅)A₆))

### Fase 4: ricostruzione soluzione

```
PRINT-OPTIMAL-PARENS(s, i, j)
  if i = j then stampa "Aᵢ"
  else stampa "("; PRINT-OPTIMAL-PARENS(s, i, s[i,j])
       PRINT-OPTIMAL-PARENS(s, s[i,j]+1, j); stampa ")"
```

## Memoizzazione (approccio top-down)

Alternativa al bottom-up: ricorsione con cache. Stessa complessità asintotica, utile quando non tutti i sottoproblemi devono essere risolti.

```
MEMOIZED-MATRIX-CHAIN(p)
  n ← length[p] – 1
  for i ← 1 to n do for j ← i to n do m[i,j] ← ∞
  return LOOKUP-CHAIN(p, 1, n)

LOOKUP-CHAIN(p, i, j)
  if m[i,j] < ∞ then return m[i,j]      ▷ già calcolato
  if i = j then m[i,j] ← 0
  else for k ← i to j–1
         q ← LOOKUP-CHAIN(p,i,k) + LOOKUP-CHAIN(p,k+1,j) + p[i–1]p[k]p[j]
         if q < m[i,j] then m[i,j] ← q
  return m[i,j]
```

## Esempio: Longest Common Subsequence (LCS)

**Problema**: date due sequenze X = ⟨x₁,…,xₘ⟩ e Y = ⟨y₁,…,yₙ⟩, trovare la sottosequenza comune più lunga.

**Sottostruttura ottima**: se xₘ = yₙ, LCS(X,Y) = LCS(Xₘ₋₁,Yₙ₋₁) + xₘ; altrimenti max tra LCS(Xₘ₋₁,Y) e LCS(X,Yₙ₋₁).

**Ricorrenza**:
```
c[i,j] = 0                            se i=0 o j=0
c[i,j] = c[i–1,j–1] + 1              se xᵢ = yⱼ
c[i,j] = max(c[i–1,j], c[i,j–1])     se xᵢ ≠ yⱼ
```

**Algoritmo LCS** bottom-up: T(n) = **Θ(mn)**, spazio Θ(mn).

## Articoli correlati

- [[ricorrenze]]
- [[introduzione-algoritmi]]
- [[grafi]]

## Fonti

- `raw/Programmazione_dinamica_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
- `raw/programmazione_dinamica_addendum.pdf` — integrazioni manoscritte
- `raw/prodotto_matriciale.pdf` — prodotto matriciale
- `raw/ricorsione_in_coda.pdf` — ricorsione in coda
