---
tags: [grafi, BFS, DFS, visita, rappresentazione, algoritmi-su-grafi]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/3-Grafi_con_integrazioni.pdf
  - raw/3-Grafi.pdf
  - raw/Pathfinding.pdf
---

# Grafi

Un **grafo** G = (V, E) è formato da un insieme di **vertici** V e da un insieme di **archi** E ⊆ V×V. Centinaia di problemi computazionali interessanti si definiscono in termini di grafi: cammini minimi, connettività, ordinamento topologico, flussi, cicli hamiltoniani.

## Punti chiave

- **Orientato**: arco (u,v) ≠ arco (v,u); v è adiacente a u ma non viceversa
- **Non orientato**: arco {u,v} = {v,u}; u e v sono mutuamente adiacenti
- **Grado**: n° archi incidenti; nei grafi orientati = grado entrante + grado uscente
- **Liste di adiacenza**: Θ(V+E) memoria; preferite per grafi sparsi
- **Matrice di adiacenza**: Θ(V²) memoria; preferita per grafi densi o verifica rapida (u,v)∈E
- **BFS**: visita in ampiezza da sorgente s; calcola distanze minime (n° archi); T = O(V+E)
- **DFS**: visita in profondità; classifica archi; scopre componenti; T = O(V+E)
- **Ordinamento topologico**: DFS su DAG, ordine inverso di fine visita; T = O(V+E)

## Terminologia

| Termine | Definizione |
|---|---|
| Cappio | Arco (u,u) con estremi coincidenti (solo in grafi orientati) |
| Grafo sparso | \|E\| ≪ \|V\|² |
| Grafo denso | \|E\| vicino a \|V\|² |
| Grafo pesato | Ogni arco ha un peso reale w(u,v) |
| Cammino di lunghezza k | Sequenza ⟨x₀,…,xₖ⟩ con (xᵢ₋₁,xᵢ) ∈ E |
| Cammino semplice | Vertici tutti distinti |
| Ciclo | Cammino con x₀=xₖ (k>0 orientato; k≥3 non orientato) |
| Grafo aciclico (DAG) | Grafo orientato senza cicli |
| Raggiungibile | v raggiungibile da u se ∃ cammino u→v |
| Connesso | Grafo non orientato con cammino tra ogni coppia |
| Fortemente connesso | Grafo orientato con u↔v raggiungibili per ogni coppia |

## Rappresentazioni

### Liste di adiacenza
Array Adj di |V| liste: Adj[u] = lista dei vertici adiacenti a u (in ordine arbitrario).
- Memoria: Θ(V+E) — Θ(E) per orientati, Θ(2E) per non orientati
- Verifica (u,v)∈E: O(grado(u)) — lineare sulla lista
- Grafi pesati: peso w(u,v) memorizzato con v nella lista

### Matrice di adiacenza
Matrice A di |V|×|V|: aᵢⱼ = 1 se (i,j)∈E, 0 altrimenti.
- Memoria: Θ(V²)
- Verifica (u,v)∈E: O(1)
- Grafi non orientati: A = Aᵀ → basta la parte sopra la diagonale
- Grafi pesati: aᵢⱼ = w(u,v) oppure NIL/∞ se arco assente

## BFS — Breadth-First Search

Visita in **ampiezza** da sorgente s: scopre tutti i vertici raggiungibili da s in ordine non decrescente di distanza.

Ogni vertice ha: `color` (WHITE/GRAY/BLACK), `d[v]` (distanza da s), `π[v]` (padre).

```
BFS(G, s)
1  for each u ∈ V–{s}: color[u] ← WHITE, d[u] ← ∞, π[u] ← NIL
2  color[s] ← GRAY, d[s] ← 0, π[s] ← NIL
3  Q ← ∅; ENQUEUE(Q, s)
4  while Q ≠ ∅
5    do u ← DEQUEUE(Q)
6       for each v ∈ Adj[u]
7         do if color[v] = WHITE
8              then color[v] ← GRAY, d[v] ← d[u]+1, π[v] ← u
9                   ENQUEUE(Q, v)
10      color[u] ← BLACK
```

**Colori**: BIANCO = non scoperto; GRIGIO = scoperto, sulla frontiera; NERO = scoperto, fuori frontiera.

**Complessità**: O(V+E) — inizializzazione O(V), ciclo while O(V) iterazioni, ispezione liste O(E) totale.

**Proprietà**: al termine, d[v] = δ(s,v) = distanza minima (n° archi) per ogni v raggiungibile.

**Albero BFS**: formato dagli archi (π[v], v) per ogni v scoperto — i cammini dalla radice s sono cammini minimi nel grafo.

**Dimostrazione** (proprietà limite superiore): d[v] ≥ δ(s,v) per induzione; si dimostra poi l'uguaglianza usando la proprietà della coda (d valori non decrescenti) e il fatto che ogni arco (u,v) con u nero ha v grigio o nero.

## DFS — Depth-First Search

Visita in **profondità**: approfondisce il più possibile prima di tornare indietro.

Ogni vertice ha: `color`, `d[v]` (tempo di scoperta), `f[v]` (tempo di fine), `π[v]`.

```
DFS(G)
1  for each u ∈ V: color[u] ← WHITE, π[u] ← NIL
2  time ← 0
3  for each u ∈ V: if color[u] = WHITE then DFS-VISIT(u)

DFS-VISIT(u)
1  color[u] ← GRAY; time ← time+1; d[u] ← time
2  for each v ∈ Adj[u]
3    do if color[v] = WHITE then π[v] ← u; DFS-VISIT(v)
4  color[u] ← BLACK; time ← time+1; f[u] ← time
```

**Complessità**: Θ(V+E).

**Parentesi**: gli intervalli [d[u], f[u]] sono o disgiunti o uno contenuto nell'altro — mai sovrapposti parzialmente.

### Classificazione degli archi (grafi orientati)

- **Archi d'albero**: (u,v) con v scoperto da u tramite DFS — forma la foresta DFS
- **Archi all'indietro**: (u,v) con v antenato di u nell'albero DFS
- **Archi in avanti**: (u,v) con v discendente di u ma non figlio diretto
- **Archi trasversali**: tutti gli altri

**Teorema**: un grafo orientato è aciclico (DAG) ↔ DFS non produce archi all'indietro.

## Ordinamento topologico

Per un **DAG** G, un ordinamento topologico è una permutazione lineare dei vertici tale che per ogni arco (u,v)∈E, u precede v.

**Algoritmo**: eseguire DFS(G), inserire ogni vertice in testa a una lista al termine (f[u] decrescente).

```
TOPOLOGICAL-SORT(G)
1  chiamare DFS(G) per calcolare i tempi f[v]
2  inserire ogni vertice in testa alla lista quando viene terminato
3  return lista
```

**Complessità**: Θ(V+E).

## Componenti fortemente connesse (SCC)

Una **SCC** di un grafo orientato è un insieme massimale di vertici u, v tali che u è raggiungibile da v e v da u.

**Algoritmo di Kosaraju**:
1. DFS(G): calcolare f[u] per tutti i vertici
2. Calcolare Gᵀ (grafo transposto)
3. DFS(Gᵀ) visitando i vertici in ordine decrescente di f[u]
4. Ogni albero della foresta DFS al passo 3 è una SCC

Complessità: Θ(V+E).

## Articoli correlati

- [[strutture-dati]]
- [[programmazione-dinamica]]
- [[complessita-computazionale]]

## Fonti

- `raw/3-Grafi_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
- `raw/3-Grafi.pdf` — versione base
- `raw/Pathfinding.pdf` — algoritmi di pathfinding
