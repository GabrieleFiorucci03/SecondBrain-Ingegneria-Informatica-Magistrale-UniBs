---
tags: [algoritmi, introduzione, complessità, divide-et-impera, pseudocodice]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Introduzione_con_integrazioni.pdf
---

# Introduzione agli Algoritmi

Disciplina che studia la rappresentazione e manipolazione dell'informazione per trovare buone soluzioni algoritmiche a problemi ben formalizzati. Riconosciuta da ACM come una delle 9 branche fondamentali della Computer Science, è trasversale a tutte le aree dell'informatica ma dotata di metodi propri.

## Punti chiave

- **Algoritmo** = procedura computazionale ben definita che trasforma un insieme di valori in input in un insieme di valori in output; nozione antecedente (papiri XVII sec. a.C.) a quella di dimostrazione matematica (Euclide, III sec. a.C.)
- **Problema** = relazione che deve valere tra input e output; un'**istanza** è un preciso valore di input
- **Algoritmo corretto** = per ogni istanza di input termina e produce l'output corretto; uno scorretto può essere utile se il tasso di errore è controllabile
- Lo studio degli algoritmi si articola in: **analisi** (correttezza + complessità), **sintesi** (progetto), **classificazione** (complessità strutturale, classi P/NP)
- La complessità si misura in modo asintotico: conta solo l'**ordine di grandezza** di T(n) per n → +∞
- Algoritmi lineari/n·lgn sono efficienti per input grandi; polinomiali (k≥3) inaccettabili; esponenziali proibitivi a prescindere dalla velocità hardware

## Definizioni formali

**Algoritmo corretto**: ∀ istanza x di input, l'algoritmo termina e restituisce l'output corretto.

**Dimensione dell'input**: misura scelta problema per problema (es. numero di elementi per l'ordinamento, coppia (nodi, archi) per i grafi).

**Complessità nel caso peggiore**: T_A^p(n) = max{ T_A(x) | |x| = n }

**Complessità nel caso medio** (input equiprobabili): T_A^m(n) = (Σ_{|x|=n} T_A(x)) / I_n

Si preferisce quasi sempre l'analisi del caso peggiore: è il limite superiore garantito, spesso il caso peggiore è frequente, e il caso medio tende comunque ad avere lo stesso ordine del peggiore (es. Insertion-Sort: T(n) = Θ(n²) in entrambi i casi).

## Modello computazionale RAM

Modello mono-processore in cui le istruzioni sono eseguite sequenzialmente (no concorrenza). Ogni riga i del pseudocodice ha costo costante c_i. Il pseudocodice adottato nel corso mescola linguaggio naturale e di programmazione: rientranze per i blocchi, `←` per l'assegnamento, `▷` per i commenti.

## Analisi asintotica

Ulteriore astrazione rispetto alla complessità concreta: si considera solo il **tasso di crescita** di T(n), ignorando coefficienti e termini di ordine inferiore.

| Complessità | Classe | Praticabilità |
|---|---|---|
| n, n·lgn | lineare / quasi-lineare | Efficiente per qualunque n |
| n² | quadratica | Accettabile per n medio |
| n³ | cubica | Inaccettabile per n grande |
| 2ⁿ, 3ⁿ | esponenziale | Proibitiva per qualunque n > ~25 |

Passare a un calcolatore M volte più veloce guadagna: ×M su input lineari, √M su input quadratici, solo +lg(M) su input esponenziali.

## Paradigmi di progetto

### Incrementale
Si risolve il problema estendendo progressivamente una soluzione parziale. Esempio: **Insertion-Sort** inserisce A[j] nella sottosequenza già ordinata A[1..j-1].

- Caso migliore (array già ordinato): T(n) = Θ(n)
- Caso peggiore (array inverso): T(n) = Θ(n²)

```
INSERTION-SORT(A)
1  for j ← 2 to length[A]
2    do key ← A[j]
3       i ← j - 1
4       while i > 0 e A[i] > key
5         do A[i+1] ← A[i]
6            i ← i - 1
7       A[i+1] ← key
```

### Divide-et-impera
A ogni livello di ricorsione: **Divide** il problema in sottoproblemi di dimensione inferiore → **Impera** risolvendo ricorsivamente → **Combina** le soluzioni parziali. Il T(n) si esprime con un'**equazione di ricorrenza**.

Esempio: **Merge-Sort** divide gli n elementi in due metà, ordina ciascuna ricorsivamente, poi fonde le due sottosequenze ordinate (MERGE lineare).

```
MERGE-SORT(A, p, r)
1  if p < r
2    then q ← ⌊(p+r)/2⌋
3         MERGE-SORT(A, p, q)
4         MERGE-SORT(A, q+1, r)
5         MERGE(A, p, q, r)
```

Ricorrenza: T(n) = Θ(1) se n=1; 2T(n/2) + Θ(n) se n>1 → **T(n) = Θ(n·lgn)**

Confronto su n=10⁶: Insertion-Sort su C1 (10⁸ istr/s) = 5,56 ore; Merge-Sort su C2 (10⁶ istr/s) = 16,67 minuti. L'algoritmo migliore batte hardware migliore.

## Articoli correlati

- [[complessita-asintotica]]
- [[ricorrenze]]
- [[algoritmi-di-ordinamento]]
- [[quicksort]]

## Fonti

- `raw/Introduzione_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
