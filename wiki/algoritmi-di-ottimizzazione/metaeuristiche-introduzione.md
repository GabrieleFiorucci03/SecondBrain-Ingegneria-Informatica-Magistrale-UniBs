---
tags: [ottimizzazione, euristiche, local-search, costruttive, metaeuristiche, TSP, k-opt]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/OA_ric_loc_2025.pdf
---

# Algoritmi Euristici e Local Search

Fondamenti degli algoritmi euristici per ottimizzazione combinatoria: costruttivi e di ricerca locale (local search). Gli algoritmi costruttivi partono da zero e aggiungono elementi; i local search partono da una soluzione ammissibile e la migliorano iterativamente. Sono il punto di partenza delle [[tabu-search|metaeuristiche]], che aggiungono tecniche per sfuggire agli ottimi locali.

## Punti chiave

- **Algoritmo euristico**: trova una buona soluzione (non necessariamente ottima) in tempo ragionevole; dal greco εὑρίσκω, "trovare o scoprire".
- **Algoritmi costruttivi**: partono da una soluzione vuota e aggiungono elementi fino alla fattibilità.
- **Local search**: partono da una soluzione ammissibile e applicano mosse nel vicinato N(i).
- Convergono a un **ottimo locale**: s tale che f(s) ≤ f(i) ∀i ∈ N(s).
- **First improvement**: si accetta la prima mossa migliorante; **best improvement** (steepest descent): si accetta la mossa che migliora di più.
- Il **2-opt** per TSP ha complessità O(n²) per iterazione; in pratica dà soluzioni ~8% peggiori dell'ottimo.
- Il **k-opt** generalizza: per k=n il vicinato diventa esatto ma di dimensione n!.
- Le **metaeuristiche** (SA, TS, GA) aggiungono tecniche per sfuggire agli ottimi locali.

## Proprietà degli algoritmi euristici

1. Il tempo di calcolo non deve crescere troppo rapidamente con la dimensione (complessità polinomiale).
2. Le soluzioni trovate devono essere, per la maggioranza delle istanze, ottime o prossime all'ottimo.
3. È un **compromesso** tra qualità della soluzione e tempo di calcolo.

**Upper Bound** per problemi di minimizzazione: un'euristica A determina un upper bound sul valore ottimo:
```
z* ≤ zᴬ ≤ z*
```
(Lower Bound per problemi di massimizzazione.)

**Due famiglie principali**:
- **Algoritmi costruttivi**: partono da una soluzione vuota, aggiungono iterativamente elementi fino a una soluzione completa ammissibile.
- **Algoritmi di local search**: partono da una soluzione ammissibile x, migliorano iterativamente applicando cambiamenti (mosse nel vicinato N(x)); terminano quando nessuna mossa migliora la soluzione (ottimo locale).

## Analisi delle prestazioni

**Due modalità di analisi**:
1. **Sperimentale**: si valuta la qualità su un benchmark di istanze.
   - Sempre eseguibile; non generalizzabile; si misura il rapporto qualità/tempo.
2. **Worst case**: si calcola analiticamente il massimo errore dall'ottimo.
   - Generalizzabile, ma difficile da determinare.

## Algoritmi costruttivi per TSP

**Problema**: TSP G(V, E), c, min.

**Schema generale**:
1. Scegli un nodo arbitrario i; soluzione parziale iniziale S := {i}.
2. Espandi S inserendo i nodi rimanenti uno alla volta per ottenere un ciclo hamiltoniano.
   - Fase 2a: **seleziona** un nodo k ∉ S secondo un criterio.
   - Fase 2b: **inserisci** k tra due nodi consecutivi u, v ∈ S.

### Criteri di selezione (fase 2a)

| Criterio | Descrizione | Errore medio | Worst case | Complessità |
|----------|-------------|-------------|-----------|-------------|
| **Nearest neighbor** | Nodo a distanza minima da S | 20% | 1 | O(n²) |
| **Farthest insertion** | Nodo a distanza massima da S | 10% | ⌈log n⌉+1 | O(n²) |
| **Arbitrary insertion** | Nodo casuale | 11% | ⌈log n⌉+1 | O(n²) |
| **Cheapest insertion** | Nodo con costo di inserimento minimo | 17% | 1 | O(n² log n) |

### Inserimento ottimale (fase 2b)

**Costo di inserzione**: per ogni (i,j) ∈ S si calcola:
```
dᵢⱼ = cᵢₖ + cₖⱼ − cᵢⱼ
```
Il nodo k viene inserito tra i nodi consecutivi u, v ∈ S tali che:
```
dᵤᵥ ≤ dᵢⱼ    ∀(i,j) ∈ S
```

## Algoritmo Greedy per TSP (Nearest Neighbor)

```
Step 1. Scegli un nodo iniziale i; W := {i}; r := i
Step 2. Seleziona s ∈ V \ W con distanza minima da r:
        d_rs = min_{j ∈ V\W} w_rj
Step 3. W := W ∪ {s}; r := s
        Se W = V: il nodo i è il successore di s (W diventa un ciclo). STOP
        Altrimenti: torna a Step 2
```

**Complessità**: O(n²) (|V| = n).

**Worst case** (4 nodi, distanza cᵢⱼ = M >> 0 per 1 arco):
- Greedy da nodo 1: seleziona 2→4→3→1 (costo 5+M); ottimo: 1→2→3→4→1 (costo 8).
- Per M ≤ 3: greedy = ottimo. Per M > 3: ratio = (5+M)/8 → ∞ per M → ∞.

## Algoritmi costruttivi per 0-1 Knapsack

**Greedy Algorithm**:
1. Ordina gli item in ordine non crescente di rapporto profitto/peso.
2. Inserisci gli item nello zaino finché il primo che supera la capacità.

**Alternativamente**: ordina per profitto non crescente o per peso non decrescente. (Worst case illimitato per tutti e tre i criteri puri, vedi [[approssimazione-algoritmi]].)

## Funzione di vicinato (Neighbourhood)

Dato un problema P = (f, S):
- S = insieme di tutte le soluzioni ammissibili per P
- f: S → ℝ funzione obiettivo

**Funzione di vicinato**: N: S → 2^|S| tale che ∀i ∈ S definisce N(i) ⊆ S (soluzioni "vicine" a i).

Gli algoritmi di local search migliorano una soluzione esplorando uno dei suoi vicinati.

## Iterative Improvement: First Improvement

```
Procedure FI_Simple_Descent(s) /* s ∈ S soluzione iniziale */
  Found := TRUE
  while Found = TRUE do
    Found := FALSE
    for each s' ∈ N(s) do
      if f(s') < f(s) then
        s := s'; Found := TRUE; break
  return s
```

Converge a un ottimo locale s rispetto a N(·): s tale che f(s) ≤ f(i) ∀i ∈ N(s).

## Iterative Improvement: Best Improvement

```
Procedure BI_Simple_Descent(s) /* s ∈ S soluzione iniziale */
  Found := TRUE
  while Found = TRUE do
    Found := FALSE; s_best := s
    for each s' ∈ N(s) do
      if f(s') < f(s_best) then
        s_best := s'
    if s_best ≠ s then
      s := s_best; Found := TRUE
  return s
```

**Metodo steepest descent**: ad ogni iterazione si sceglie la mossa che produce il miglioramento maggiore.

**Svantaggio**: ogni iterazione richiede la valutazione di f(s) per O(|N(s)|) soluzioni → numero di iterazioni per raggiungere l'ottimo locale potenzialmente molto alto (anche se polinomiale).

## Local Search: vantaggi, svantaggi, requisiti

| Aspetto | Contenuto |
|---------|-----------|
| **Vantaggi** | Ampia applicabilità; flessibilità rispetto a cambiamenti del problema; utilizzabile anche quando la soluzione non è ammissibile |
| **Svantaggi** | Non possono sfuggire ai minimi locali (non ammettono mosse peggioranti) |
| **Requisiti** | Solution evaluator (funzione obiettivo); feasibility check; neighbourhood function; tecnica di esplorazione efficiente del vicinato |

## k-opt heuristics per TSP

### 2-opt

**Basic idea**: a ogni step si rimuovono 2 archi e i due path vengono riconnessi con 2 archi diversi.

```
Algoritmo:
1. Parti da un ciclo hamiltoniano
2. Esegui swap 2-opt tra tutte le coppie di archi che riducono la lunghezza del circuito
```

**Efficacia**: in media ~8% peggiore del minimo.

**Visualizzazione** (3 step):
- Step 1: rimuovi 2 archi → due path → riconnetti invertendo la direzione di uno dei path.
- Step 2: rimuovi altri 2 archi → riconnetti.
- Step 3: ultimo swap → soluzione finale migliorata.

### 3-opt

**Basic idea**: a ogni step si rimuovono 3 archi e i 3 path vengono riconnessi con 3 archi diversi.

**Efficacia**: in media ~4% peggiore del minimo.

**Generalizzazione — k-opt**: per k=n il vicinato diventa esatto e contiene tutti i possibili cicli hamiltoniani (n! soluzioni).

## Metaeuristiche: motivazione

**Problema dei local search**: rischiano di rimanere bloccati in ottimi locali che non sono ottimi globali.

**Soluzione — algoritmi metaeuristici**:
- Algoritmi di local search che usano tecniche speciali per sfuggire ai minimi locali.
- Algoritmi che evitano i cicli (ripetizione di soluzioni già visitate).

**Esempi**:
- **Simulated Annealing (SA)**: accetta mosse verso soluzioni peggiori con probabilità decrescente.
- **Tabu Search (TS)**: ammette mosse verso soluzioni peggiori; evita soluzioni già viste tramite una lista di mosse proibite (tabu).
- **Genetic Algorithms (GA)**: algoritmi evolutivi basati su crossover e mutazione.

## Articoli correlati

- [[tabu-search]]
- [[grasp]]
- [[vns]]
- [[alns]]
- [[approssimazione-algoritmi]]
- [[ottimizzazione-combinatoria]]

## Fonti

- `raw/OA_ric_loc_2025.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2024/2025.
