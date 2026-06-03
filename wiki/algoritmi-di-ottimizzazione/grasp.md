---
tags: [ottimizzazione, metaeuristica, grasp, local-search, greedy, randomizzazione, multi-start]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/grasp.pdf
---

# GRASP

**Greedy Randomized Adaptive Search Procedure**. Metaeuristica multi-start e iterativa che combina costruzione greedy randomizzata (Fase I) con ricerca locale (Fase II). Classificazione: **M/N/1** (memory-less, neighbourhood, 1). Introdotta da Feo e Resende (1989) per problemi di set covering.

## Punti chiave

- Ogni iterazione GRASP produce una soluzione; la migliore tra tutte le iterazioni è il risultato finale.
- **Fase I** (BuildSolution): costruisce una soluzione ammissibile greedy, randomizzata e adattiva usando la **Restricted Candidate List (RCL)**.
- **Fase II** (LocalSearch): migliora la soluzione con una ricerca locale nel vicinato N(S).
- La **RCL** bilancia greedy e random: α, β, γ parametri che controllano quanti candidati includere.
- **Adattivo**: la funzione rank θ_S dipende dalla soluzione parziale corrente, non è statica.
- **Parallelizzabile** facilmente: ogni processo GRASP è indipendente (easy way) o con comunicazione inter-processo (hard way).
- Pro: framework semplice, pochi parametri, scalabile. Contro: può valutare la stessa soluzione più volte, difficile sfuggire a ottimi locali.

## Schema dell'algoritmo

```
function GRASP(instance):
  bestSolution ← null
  bestObjValue ← null
  while stopping rule is not satisfied do
    solution ← BuildSolution(instance)
    newSolution, newObjValue ← LocalSearch(instance, solution)
    if newObjValue is better than bestObjValue then
      bestSolution ← newSolution
      bestObjValue ← newObjValue
  return bestSolution, bestObjValue
```

**Stopping rule**: numero di iterazioni, tempo trascorso, o parametri significativi del problema.

## Fase I — BuildSolution

La procedura **deve essere**:
1. **Greedy**: nuovi item selezionati tra quelli che offrono il miglior miglioramento dell'obiettivo.
2. **Random** (probabilistico): la selezione non è puramente greedy; avviene tra i migliori candidati della RCL.
3. **Adaptive**: la scelta del prossimo item è influenzata da quelli già scelti (θ_S dipende da S).

### Funzione rank

Ogni item fuori dalla soluzione corrente è ordinato dalla funzione rank:
```
θ: Items → ℝ        (versione statica)
θ_S: Items → ℝ      (versione adattiva, dipende dalla soluzione parziale S)
```

### Greedy puro (BuildSolution — Algoritmo 2)

```
function BuildSolution(instance):
  solution ← null
  sortedItems ← Sort(instance.items, θ)
  while solution is not feasible do
    nextItem ← RemoveFirst(sortedItems)
    solution ← Add(solution, nextItem)
  return solution
```
**Problema**: deterministica → ritorna sempre la stessa soluzione in GRASP multi-start.

### Adattivo (Algoritmo 4)

```
function BuildSolution(instance):
  solution ← null
  while solution is not feasible do
    sortedItems ← Sort(instance.items, θ_solution)  ← ricalcola ad ogni step
    nextItem ← RemoveFirst(sortedItems)
    solution ← Add(solution, nextItem)
  return solution
```
Più costosa della versione statica, ma produce soluzioni migliori.

### Restricted Candidate List (RCL)

Per introdurre randomizzazione, si costruisce la RCL con i "migliori candidati" e si sceglie casualmente tra essi.

**Parametri**:
- **α ∈ [0,1]**: considera gli elementi che aumentano l'obiettivo al più α volte rispetto al best. (α=0: puramente greedy; α=+∞: puramente random).
- **β ∈ [0,1]**: considera i β·|A| migliori elementi. (β=0: puramente greedy; β=1: puramente random).
- **γ ∈ [1,n]**: considera al più i top-γ elementi. (γ=1: puramente greedy; γ=n: puramente random).

```
function BuildRCL(items, S, θ_S, α, β, γ):
  A ← items \ S
  a ← min{ θ_S(i) | i ∈ A }
  b ← max{ θ_S(i) | i ∈ A }
  RCL ← { i ∈ A | θ_S(i) ≤ a + α(b − a) }
  k ← min{ γ, β·|A| }
  RCL ← TopK(RCL, k)
  return RCL
```

### GRASP Phase I completa (Algoritmo 7)

```
function BuildSolution(instance):
  solution ← null
  while solution is not feasible do
    RCL ← BuildRCL(instance, S, θ_solution, α, β, γ)
    chosenItem ← ChooseRandomItem(RCL)
    solution ← Add(solution, chosenItem)
  return solution
```

## Esempio: Set Covering Problem (SCP)

**Problema**: dato U = {1,...,m}, collezione U di sottoinsiemi Uⱼ ⊆ U con costo cⱼ, trovare S ⊆ N con costo minimo tale che ∪ⱼ∈S Uⱼ = U.

**Rank function statica**: `θ(j) = cⱼ / |Uⱼ|`

**Rank function adattiva**: `θ_S(j) = cⱼ / |Uⱼ \ C_S|` dove C_S = ∪ⱼ∈S Uⱼ (divide per gli elementi non ancora coperti).

| Variante | Soluzione (U={1,..,16}, U={U₁,U₂,U₃,U₄}) | Valore |
|----------|------------------------------------------|--------|
| Greedy statico (3 subset) | S = {1,2,3} | 290 |
| Greedy statico (4 subset) | S = {1,2,3,4} | 338 (peggiore!) |
| Adattivo | S = {1,3,2} | 290 |

L'adattivo evita il "bad behaviour" del greedy statico quando vengono introdotti nuovi subset.

**SCP adattivo — Algoritmo 5**:
```
function BuildSolution(U, U, {cⱼ}ⱼ∈N):
  S ← ∅
  while ∪ⱼ∈S Uⱼ ≠ U do
    j ← argminⱼ∈N\S { θ_S(Uⱼ) }
    S ← S ∪ {j}
  return S
```

**SCP GRASP Phase I — Algoritmo 8**:
```
function BuildSolution(U, U, {cⱼ}ⱼ∈N):
  S ← ∅
  while ∪ⱼ∈S Uⱼ ≠ U do
    RCL ← BuildRCL(N, S, θ_S, α, β, γ)
    j ← ChooseRandom(RCL)
    S ← S ∪ {j}
  return S
```

## Fase II — LocalSearch

L'obiettivo è **migliorare** la soluzione S ottenuta dalla Fase I tramite ricerca locale nel vicinato N(S).

```
function LocalSearch(instance, solution):
  while solution is not locally optimal in N(solution) do
    newSolution ← FindBetterSolution(N(solution))
    solution ← newSolution
  return solution
```

La definizione di N(S) è **problem-dependent**: diverse strategie per lo stesso problema possono portare a risultati molto diversi.

### SCP: vicinato (r,s)-exchange

Una **(r,s)-exchange move** rimuove r subset da S e li rimpiazza con s subset da N \ S, preservando la fattibilità.

```
N_{a,b}(S) := { m(S) | (r,s)-exchange move, 0 ≤ r ≤ a, 0 ≤ s ≤ b }
```

Tipicamente si usa N_{1,1}(S) (rimuovi 1, aggiungi 1).

**SCP GRASP Phase II — Algoritmo 10**:
```
function LocalSearch(U, U, {cⱼ}ⱼ∈N, S):
  while f(S) ≠ min{ f(T) | T ∈ N_{1,1}(S) } do
    S' ← argminT∈N₁,₁(S) { f(T) }
    S ← S'
  return S
```

## Considerazioni avanzate

### GRASP parallelo

**Easy way**: spawna N processi indipendenti, ognuno esegue GRASP completo. La randomicità della Fase I garantisce che ogni processo esplori soluzioni (per lo più) distinte. Risultato finale: il migliore tra tutti i processi.

**Hard way**: condivisione di informazioni tra processi per evitare computazioni duplicate (es. rank function per la RCL). Altamente dipendente dai dettagli del problema.

### Flessibilità: SCP con Conflitti (SCP-CS)

Aggiungere costi di conflitto `dⱼₗ` tra coppie di subset {Uⱼ, Uₗ} richiede solo di aggiornare la rank function in Fase I:
```
θ_S(j) = (cⱼ + Σₗ∈S dⱼₗ) / |Uⱼ \ C_S|
```
La Fase II rimane invariata (la fattibilità è la stessa, cambia solo f).

## Pro e Contro

| Pro | Contro |
|-----|--------|
| Framework semplice (facile da implementare) | La stessa soluzione può essere valutata più volte |
| Greedy esistenti migliorabili con GRASP | Sfuggire agli ottimi locali può essere difficile |
| Gestisce problemi di grande dimensione | |
| Pochi parametri da tuning (stopping rule + RCL) | |
| Parallelizzabile (trivialmente o in modo sofisticato) | |

## Articoli correlati

- [[tabu-search]]
- [[vns]]
- [[alns]]
- [[metaeuristiche-introduzione]]
- [[approssimazione-algoritmi]]

## Fonti

- `raw/grasp.pdf` — slide del corso Optimization Algorithms, Lorenzo Moreschini, UniBS 21 Novembre 2025.
