---
tags: [quicksort, ordinamento, divide-et-impera, randomizzato, analisi-caso-medio]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Quicksort_con_integrazioni.pdf
  - raw/Appendice Quicksort.pdf
  - raw/quicksort_addendum.pdf
---

# Quicksort

Algoritmo di ordinamento basato su **divide-et-impera**, in-loco. Il cuore è PARTITION che sceglie un **perno** (pivot) e ripartisce l'array in due sottoarray: elementi ≤ perno e elementi ≥ perno. Non richiede la fase di combinazione (gli array sono già ordinati una volta risolti i sottoproblemi).

## Punti chiave

- **Caso migliore**: T(n) = Θ(n) + 2T(n/2) = **Θ(n·lg n)** — partizionamento perfettamente bilanciato
- **Caso peggiore**: T(n) = Θ(n) + T(n–1) = **Θ(n²)** — si verifica quando l'array è già ordinato (perno sempre il minimo o massimo)
- **Caso medio**: **Θ(n·lg n)** — persino con partizionamento 9:1 costante si ottiene Θ(n·lg n)
- La versione **randomizzata** (RANDOMIZED-PARTITION) evita che il caso pessimo dipenda dall'input, rendendolo funzione del generatore casuale
- In pratica il miglior algoritmo di ordinamento per input di grandi dimensioni con tutte le permutazioni equiprobabili
- Differenza da Merge-Sort: combina in modo triviale ma divide in modo non banale (il viceversa di Merge-Sort)

## Pseudocodice

```
QUICKSORT(A, p, r)
1  if p < r
2    then q ← PARTITION(A, p, r)    ▷ p ≤ q < r
3         QUICKSORT(A, p, q)
4         QUICKSORT(A, q+1, r)
```

Chiamata iniziale: QUICKSORT(A, 1, length[A])

## PARTITION (di Hoare)

```
PARTITION(A, p, r)
1  x ← A[p]          ▷ x è il perno
2  i ← p – 1
3  j ← r + 1
4  while TRUE
5    do  repeat j ← j – 1 until A[j] ≤ x   ▷ ≤ (non <) per gestire perno = min
6        repeat i ← i + 1 until A[i] ≥ x   ▷ ≥ (non >) per gestire perno = max
7        if i < j
8          then scambia A[i] ↔ A[j]
9          else return j
```

Costo: Θ(n). Postcondizione: A[p..q] ≤ A[q+1..r] e p ≤ q < r.

**Esempio**: [5,3,2,6,4,1,3,7] con perno=5 → [3,3,2,1,4|6,5,7] (q=4 restituito)

## Analisi della complessità

**Caso peggiore** — partizione 1:(n–1) ad ogni passo:
T(n) = Θ(n) + T(n–1) + T(1) = Σ_{k=1}^{n} Θ(k) = **Θ(n²)**

Accade quando A è già ordinato (Partition sceglie sempre il minimo come perno). Paradossalmente, è il caso migliore per Insertion-Sort.

**Caso migliore** — partizione n/2:n/2:
T(n) = Θ(n) + 2T(n/2) = **Θ(n·lg n)**

**Caso medio** — qualsiasi suddivisione di proporzionalità costante:
- Partizionamento 9:1: T(n) = n + T(9n/10) + T(n/10) = **Θ(n·lg n)**
- Alternanza di partizioni buone e cattive → risultato sempre Θ(n·lg n)
- Motivo: le partizioni buone "assorbono" il costo delle cattive

## Versione randomizzata

```
RANDOMIZED-PARTITION(A, p, r)
1  i ← RANDOM(p, r)     ▷ sceglie perno casuale
2  scambia A[p] ↔ A[i]
3  return PARTITION(A, p, r)

RANDOMIZED-QUICKSORT(A, p, r)
1  if p < r
2    then q ← RANDOMIZED-PARTITION(A, p, r)
3         RANDOMIZED-QUICKSORT(A, p, q)
4         RANDOMIZED-QUICKSORT(A, q+1, r)
```

Proprietà: il caso peggiore dipende dal generatore casuale, non dall'input. Con tutti i numeri distinti, il perno ha probabilità 1/n di avere qualunque rango. L'analisi del caso medio fornisce **E[T(n)] = O(n·lg n)**.

## Analisi probabilistica del partizionamento

Con RANDOMIZED-PARTITION e tutti gli elementi distinti:
- P(perno = min) = P(rango=1) = 1/n → partizione 1:(n–1) 
- P(perno = k-esimo) = 1/n per ogni k → lato basso contiene k–1 elementi

Ricorrenza del caso medio:
E[T(n)] = (1/n) · Σ_{k=1}^{n} (T(k–1) + T(n–k) + Θ(n)) = **O(n·lg n)**

## Confronto algoritmi di ordinamento basati su confronti

| Algoritmo | T caso peggiore | T caso medio | In-loco |
|---|---|---|---|
| Insertion-Sort | O(n²) | O(n²) | Sì |
| Merge-Sort | O(n·lg n) | O(n·lg n) | No |
| Heap-Sort | O(n·lg n) | O(n·lg n) | Sì |
| Quicksort | O(n²) | O(n·lg n) | Sì |

Quicksort è preferito nella pratica nonostante il caso pessimo Θ(n²), perché le costanti nascoste nel Θ(n·lg n) sono molto piccole e il caso pessimo è rarissimo con la versione randomizzata.

## Articoli correlati

- [[algoritmi-di-ordinamento]]
- [[ricorrenze]]
- [[ordinamento-lineare]]

## Fonti

- `raw/Quicksort_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
- `raw/quicksort_addendum.pdf` — integrazioni manoscritte
- `raw/Appendice Quicksort.pdf` — appendice aggiuntiva
