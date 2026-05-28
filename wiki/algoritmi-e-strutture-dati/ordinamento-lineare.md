---
tags: [ordinamento, counting-sort, radix-sort, bucket-sort, limite-inferiore, albero-decisione]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Ord_tempo_lineare_con_integrazioni.pdf
---

# Ordinamento in Tempo Lineare

Gli algoritmi di ordinamento basati su confronti hanno un **limite inferiore** di Ω(n·lg n). Per superarlo occorre sfruttare informazioni aggiuntive sulle chiavi (intervallo noto, distribuzione uniforme, struttura a cifre). Gli algoritmi qui descritti operano tutti in O(n) sotto opportune ipotesi.

## Punti chiave

- **Limite inferiore** per confronti: qualunque algoritmo basato su confronti richiede Ω(n·lg n) confronti nel caso peggiore — dimostrato tramite alberi di decisione
- **Counting-Sort**: chiavi intere in [1..k]; T(n) = O(n+k); stabile; non in-loco
- **Radix-Sort**: chiavi a d cifre in [1..k]; T(n) = O(d(n+k)) = O(n) se d, k costanti; richiede un sotto-ordinamento stabile
- **Bucket-Sort**: chiavi in [0,1) distribuite uniformemente; E[T(n)] = O(n); utilizza Insertion-Sort sui bucket

## Limite inferiore: albero di decisione

Un **albero di decisione** rappresenta tutte le possibili sequenze di confronti di un algoritmo su n elementi:
- Nodo interno: confronto aᵢ ≤ aⱼ
- Foglia: permutazione soluzione (≥ n! foglie per n elementi distinti)
- Altezza = n° confronti nel caso peggiore

**Teorema**: qualunque albero di decisione che ordina n elementi ha altezza Ω(n·lg n).

**Dimostrazione**: un albero binario di altezza h ha al più 2ʰ foglie → n! ≤ 2ʰ → h ≥ lg(n!) = Θ(n·lg n) (approssimazione di Stirling) → h = Ω(n·lg n).

**Corollario**: Merge-Sort e Heapsort sono asintoticamente ottimi tra gli algoritmi per confronti.

## Counting-Sort

**Ipotesi**: ogni elemento di A è un intero in [1..k].

**Idea**: per ogni x ∈ A, contare quanti elementi ≤ x; questo dice la posizione di x nell'output.

```
COUNTING-SORT(A, B, k)
1  for i ← 1 to k do C[i] ← 0           ▷ O(k)
2  for j ← 1 to n do C[A[j]] ← C[A[j]]+1 ▷ O(n): C[i] = n° elementi = i
3  for i ← 2 to k do C[i] ← C[i]+C[i–1]  ▷ O(k): C[i] = n° elementi ≤ i
4  for j ← n downto 1                      ▷ O(n): piazza A[j] in B
5    do B[C[A[j]]] ← A[j]
6       C[A[j]] ← C[A[j]] – 1
```

**Analisi**:
- T(n) = O(k+n); se k = O(n) allora **T(n) = O(n)**
- **Stabile**: elementi con lo stesso valore mantengono l'ordine relativo dell'input (il ciclo 4 scorre da destra — j downto 1)
- Non in-loco: richiede array ausiliario B e C

La stabilità è essenziale quando gli elementi hanno campi satellite e quando Counting-Sort è usato come sotto-ordinamento in Radix-Sort.

## Radix-Sort

Ordina n elementi a d cifre (dalla meno significativa alla più significativa) usando un **ordinamento stabile** per ciascuna cifra.

```
RADIX-SORT(A, d)
1  for i ← 1 to d
2    do usa un ordinamento stabile per ordinare A sulla cifra i
```

Con Counting-Sort come sotto-ordinamento (cifre in [1..k]):
- Ogni passata: O(n+k)
- d passate: **T(n) = O(d(n+k))**
- Se d costante e k = O(n): **T(n) = O(n)**

**Nota storica**: originariamente usato per ordinare schede perforate (12 righe × 80 colonne); l'ordinamento dalla cifra meno significativa permette di usare una macchina con una sola colonna per passata.

Applicabile anche a record con chiavi multiple: ordinare prima per chiave meno significativa, poi per le successive in modo stabile.

## Bucket-Sort

**Ipotesi**: input generato uniformemente in [0,1) (generalizzabile a [a, b)).

**Idea**: dividere [0,1) in n bucket di uguale dimensione, distribuire gli elementi, ordinare ogni bucket con Insertion-Sort, concatenare.

```
BUCKET-SORT(A)
1  n ← length[A]
2  for i ← 1 to n do inserisci A[i] in B[⌊nA[i]⌋]   ▷ O(n)
3  for i ← 0 to n–1 do ordina B[i] con Insertion-Sort  ▷ O(nᵢ²)
4  concatena B[0], B[1], .., B[n–1]                    ▷ O(n)
```

**Analisi caso medio**: E[T(n)] = O(n). Il costo atteso totale dei Insertion-Sort sui bucket è O(n) perché, con distribuzione uniforme, il numero atteso di elementi per bucket è costante: E[nᵢ²] = 2 – 1/n → Σ E[nᵢ²] = O(n).

## Confronto algoritmi di ordinamento

| Algoritmo | T(n) | In-loco | Stabile | Ipotesi chiavi |
|---|---|---|---|---|
| Insertion-Sort | O(n²) | Sì | Sì | Nessuna |
| Merge-Sort | O(n·lg n) | No | Sì | Nessuna |
| Heapsort | O(n·lg n) | Sì | No | Nessuna |
| Quicksort | O(n·lg n) medio | Sì | No | Nessuna |
| Counting-Sort | O(n+k) | No | Sì | Interi in [1..k] |
| Radix-Sort | O(d(n+k)) | No | Sì | d cifre in [1..k] |
| Bucket-Sort | O(n) medio | No | — | Uniforme in [0,1) |

## Articoli correlati

- [[algoritmi-di-ordinamento]]
- [[quicksort]]
- [[mediano-e-selezione]]

## Fonti

- `raw/Ord_tempo_lineare_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
