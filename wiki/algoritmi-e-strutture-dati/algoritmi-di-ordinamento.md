---
tags: [ordinamento, heap, heapsort, insertion-sort, merge-sort, algoritmi]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Ordinamento_con_integrazioni.pdf
  - raw/ordinamento_addendum.pdf
---

# Algoritmi di Ordinamento

Panoramica degli algoritmi di ordinamento basati su confronti. Il problema consiste nell'ordinare n elementi in ordine non decrescente. Quando si ordinano record con un campo **chiave**, è più efficiente permutare un array di puntatori ai record piuttosto che i record stessi.

## Punti chiave

- **Insertion-Sort**: O(n²) caso peggiore, O(n) caso migliore, in-loco, incrementale
- **Merge-Sort**: O(n·lg n) sempre, non in-loco, divide-et-impera
- **Heap-Sort**: O(n·lg n) sempre, **in-loco** — combina i vantaggi dei due precedenti
- La struttura **heap binario** è un albero binario quasi completo memorizzato in un array
- **BUILD-HEAP** costruisce uno heap in O(n) (non O(n·lg n) come sembrerebbe)
- **Coda con priorità**: applicazione principale dello heap; operazioni INSERT e EXTRACT-MAX in O(lg n)
- Limite inferiore per algoritmi basati su confronti: Ω(n·lg n)

## Heap binario

Un **heap** (diverso dall'heap della memoria) è una struttura dati memorizzata in un array A con due attributi:
- `length[A]` = n° elementi di A
- `heap-size[A]` = n° elementi dello heap, con heap-size[A] ≤ length[A]

**Heap binario**: albero binario quasi completo — tutti i livelli sono pieni tranne eventualmente l'ultimo, riempito da sinistra.

- Radice = A[1]; nodo i → figlio sinistro = A[2i], figlio destro = A[2i+1], padre = A[⌊i/2⌋]
- **Proprietà dello heap** (max-heap): ∀i > 1, A[PARENT(i)] ≥ A[i] → il massimo è in A[1]
- Altezza = ⌊lg n⌋ = Θ(lg n); tutte le operazioni base: O(lg n)

**Esempio**: A = ⟨16,14,10,8,7,9,3,2,4,1,5,15⟩ con heap-size=10

## HEAPIFY

Ripristina la proprietà dello heap su un sottoalbero con radice in A[i], assumendo che i sottoalberi sinistro e destro siano già heap.

```
HEAPIFY(A, i)
1  l ← LEFT(i);  r ← RIGHT(i)
2  if l ≤ heap-size[A] e A[l] > A[i]
3    then largest ← l  else largest ← i
4  if r ≤ heap-size[A] e A[r] > A[largest]
5    then largest ← r
6  if largest ≠ i
7    then scambia A[i] ↔ A[largest]
8         HEAPIFY(A, largest)
```

Ricorrenza: T(n) ≤ T(2n/3) + Θ(1) → **T(n) = O(lg n)** (caso 2 del Master con b=3/2, a=1)

## BUILD-HEAP

Costruisce uno heap a partire da un array arbitrario, chiamando HEAPIFY sugli elementi non-foglia da sinistra a destra (le foglie A[⌊n/2⌋+1 .. n] sono già heap da sole).

```
BUILD-HEAP(A)
1  heap-size[A] ← length[A]
2  for i ← ⌊length[A]/2⌋ downto 1
3    do HEAPIFY(A, i)
```

**Analisi**: sembra O(n·lg n) ma è **O(n)** perché i nodi agli ultimi livelli (che sono la maggioranza) hanno altezza bassa:
T(n) = Σ_{h=0}^{⌊lg n⌋} ⌈n/2^{h+1}⌉ · O(h) = O(n · Σ_{h=0}^{∞} h/2^h) = O(n · 2) = **O(n)**

## HEAPSORT

Ordina un array di n elementi in O(n·lg n) in-loco:

```
HEAPSORT(A)
1  BUILD-HEAP(A)            ← O(n)
2  for i ← length[A] downto 2
3    do scambia A[1] ↔ A[i]
4       heap-size[A] ← heap-size[A] – 1
5       HEAPIFY(A, 1)       ← O(lg n)
```

T(n) = O(n) + (n–1)·O(lg n) = **O(n·lg n)**

Idea: il massimo è sempre in A[1]; lo si estrae portandolo in fondo, si riduce lo heap, si ripristina la proprietà. Alla fine A è ordinato in modo non decrescente.

## Coda con priorità

Applicazione principale dello heap: mantiene un insieme S di elementi con chiave attraverso:

| Operazione | Costo | Descrizione |
|---|---|---|
| MAXIMUM(S) | O(1) | Restituisce il massimo (= A[1]) |
| EXTRACT-MAX(S) | O(lg n) | Rimuove e restituisce il max |
| INSERT(S, key) | O(lg n) | Inserisce con la chiave data |

**HEAP-EXTRACT-MAX**: salva A[1], porta l'ultimo in cima, decrementa heap-size, chiama HEAPIFY(A,1).

**HEAP-INSERT**: aggiunge in fondo, poi "risale" verso la radice finché la proprietà è soddisfatta (cammino di lunghezza O(lg n)).

Applicazioni: scheduling di processi (chiave = priorità), simulazione event-driven (chiave = istante evento).

## Confronto algoritmi di ordinamento basati su confronti

| Algoritmo | T(n) caso peggiore | In-loco | Paradigma |
|---|---|---|---|
| Insertion-Sort | O(n²) | Sì | Incrementale |
| Merge-Sort | O(n·lg n) | No | Divide-et-impera |
| Heap-Sort | O(n·lg n) | Sì | Heap |

Heapsort combina i vantaggi di Insertion-Sort (in-loco) e Merge-Sort (O(n·lg n)).

## Limite inferiore per il sorting

Qualsiasi algoritmo di ordinamento basato su confronti esegue Ω(n·lg n) confronti nel caso peggiore — vedi [[ordinamento-lineare]] per gli algoritmi che superano questo limite usando ipotesi aggiuntive sulle chiavi.

## Articoli correlati

- [[ricorrenze]]
- [[introduzione-algoritmi]]
- [[quicksort]]
- [[ordinamento-lineare]]
- [[strutture-dati]]

## Fonti

- `raw/Ordinamento_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS (heap, heapsort, priority queue)
- `raw/ordinamento_addendum.pdf` — integrazioni manoscritte (albero heap, Build-Heap, Heapsort analisi)
