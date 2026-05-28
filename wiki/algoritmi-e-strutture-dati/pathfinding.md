---
tags: [pathfinding, A-star, dijkstra, euristiche, gridmap, JPS, CPD]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Pathfinding.pdf
---

# Pathfinding

Il **pathfinding** studia algoritmi per trovare cammini ottimi in mappe discretizzate (es. videogiochi, robotica, navigazione). La mappa viene convertita in un grafo: le celle percorribili sono vertici, le celle adiacenti sono connesse da archi con peso 1 (mosse cardinali) o √2 (mosse diagonali).

## Punti chiave

- **Dijkstra**: cammini minimi da sorgente su grafo pesato con pesi non negativi; ottimale ma espande molti nodi
- **Greedy Best-First Search**: f(n)=h(n); veloce ma non ottimale
- **A\***: f(n)=g(n)+h(n); ottimale se h ammissibile; bilancia costo percorso e stima euristica
- **Euristica ammissibile**: h(n) ≤ h*(n) (non sovrastima mai); condizione necessaria per ottimalità di A*
- **JPS (Jump Point Search)**: accelera A* potando i vicini simmetrici; ottimale come A*
- **CPD (Compressed Path Database)**: pre-calcola first-move map compressa; query O(1) ammortizzato
- Gridmap 4-connected: mosse cardinali costo 1 → euristica Manhattan; 8-connected: diagonali costo √2 → euristiche Chebyshev/Diagonale

## Mappe come grafi

Una **gridmap** è una griglia in cui ogni cella è un nodo:
- **4-connected**: 4 vicini cardinali (N,S,E,W), costo 1
- **8-connected**: 8 vicini (cardinali + diagonali), costo cardinale=1, diagonale=√2

Ostacoli = nodi rimossi o archi non percorribili.

## Dijkstra su gridmap

Espande i nodi per distanza crescente dalla sorgente (BFS su grafo pesato con coda con priorità). Garantisce la **soluzione ottima** ma esplora in tutte le direzioni senza informazione sul goal → molti nodi espansi.

**Ottimizzazione**: terminare anzitempo quando nella coda ci sono solo vertici v con d[v] > C* (costo ottimo trovato).

## Strategie di ricerca informata

Usano una **funzione euristica** h(n) = costo stimato del cammino meno costoso dallo stato n al goal.

**Schema generale (Best-First Search)**:
```
BESTFIRSTSEARCH(start, target)
  add start to queue
  while queue not empty:
    current ← vertex of queue with min f value
    if current = target: return current
    for each neighbor n of current not visited:
      mark n visited; add n to queue
```

### Greedy Best-First Search
f(n) = h(n)

- Va verso il nodo più vicino all'obiettivo secondo l'euristica
- **Completo** (se spazio finito)
- **Non ottimale** in generale
- T = O(bᵐ), spazio O(bᵐ) — b branching factor, m lunghezza max percorso

### A* Search
f(n) = g(n) + h(n)

- g(n) = costo reale dal nodo iniziale a n
- h(n) = stima euristica da n al goal
- f(n) = stima del costo totale del percorso migliore che passa per n

**Teorema**: se h è ammissibile, A* trova la soluzione ottima.

**Dimostrazione**: per assurdo, supponiamo A* restituisca costo C > C*. Esiste n su un percorso ottimo non espanso → f(n) > C* (altrimenti sarebbe stato espanso). Ma f(n) = g*(n) + h(n) ≤ g*(n) + h*(n) = C*. Contraddizione.

**Proprietà**:
- Completo: Sì
- Ottimale: Sì (se h ammissibile)
- T = O(b^{1+⌈C*/ε⌉}) caso peggiore
- A* espande tutti i nodi con f(n) < C*, basta una espansione per nodo se h è **consistente** (h(n) ≤ w(n,n') + h(n') per ogni arco (n,n'))

## Euristiche per gridmap

| Euristica | Formula | Adatta per | Ammissibile |
|---|---|---|---|
| Manhattan | D·(\|dx\|+\|dy\|) | 4-connected | Sì |
| Chebyshev | D·max(\|dx\|,\|dy\|) | 8-connected | Sì (ma non coincide con dist. reale) |
| Diagonale | D·(\|dx\|+\|dy\|)+(D2–2D)·min(\|dx\|,\|dy\|) | 8-connected | Sì, coincide con dist. reale |
| Euclidea | D·√(dx²+dy²) | any-angle | Sì ma inaccurata per 4/8-connected |

Dove D = costo mossa cardinale, D2 = costo mossa diagonale (≈1.414).

## Jump Point Search (JPS)

Accelera A* **potando** i vicini simmetrici: si saltano i nodi che sono raggiungibili più efficientemente dal genitore di x senza passare per x.

**Vicini naturali** di x (direzione d): nodi che restano dopo il pruning — solo quelli per cui il percorso ottimo passa necessariamente per x.

**Vicini forzati**: nodi non-naturali che non possono essere ignorati a causa di ostacoli nelle vicinanze — quando un ostacolo blocca il percorso diretto dal genitore.

**Jump point**: nodo con almeno un vicino forzato; è l'unico tipo di nodo aggiunto alla coda di A*.

**Funzione jump(x, d, s, g)**: esegue la ricerca in direzione d da x fino a trovare un jump point, il goal, o un ostacolo.

**Ottimalità**: A* con JPS è ottimale perché ogni svolta su un percorso ottimo diagonal-first è un jump point, quindi viene sempre trovata.

**Vantaggio pratico**: drastica riduzione del numero di nodi espansi rispetto ad A* standard, mantenendo l'ottimalità.

## Compressed Path Database (CPD)

Approccio **offline + online**:
1. **Fase offline**: per ogni nodo n, calcolare T(n) = first-move map (via Dijkstra modificato) e comprimerla in L(n) = lista di rettangoli omogenei ordinati
2. **Fase online**: rispondere a query (s→t) in O(|L(z)|) per hop

**First-move map T(n)**: per ogni destinazione t, indica la prima mossa ottima da n verso t. Comprimibile perché aree contigue hanno spesso la stessa first-move.

**Compressione**: si identificano rettangoli omogenei (stessa first-move) tramite decomposizione ricorsiva. Compress riduce lo spazio fino a fattore 100.

**Costi**: preprocessing O(n² log n); query O(|L(z)|) ammortizzato ≈ O(1) in pratica.

```
Runtime pathfinding(s, t):
  z ← s
  while z ≠ t:
    m ← getMove(L(z), t)    ▷ cerca t nei rettangoli di L(z)
    z ← γ(z, m)             ▷ esegui mossa
    π ← π ⊕ m
  return π
```

## Confronto algoritmi

| Algoritmo | Ottimale | Preprocessing | Query | Note |
|---|---|---|---|---|
| Dijkstra | Sì | No | O(V log V + E) | Esplora tutto |
| Greedy BFS | No | No | O(bᵐ) | Veloce, non ottimale |
| A* | Sì (h amm.) | No | O(b^{C*/ε}) | Bilanciato |
| JPS | Sì | No | < A* | Pota simmetrie |
| CPD | Sì | O(n² log n) | O(1) amm. | Ideale per query ripetute |

## Articoli correlati

- [[grafi]]
- [[algoritmi-di-ordinamento]]
- [[programmazione-dinamica]]

## Fonti

- `raw/Pathfinding.pdf` — Prof. Alessandro Saetti, UniBS
