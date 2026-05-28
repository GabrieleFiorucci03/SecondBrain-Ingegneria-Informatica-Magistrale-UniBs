---
tags: [indice, algoritmi, strutture-dati]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
---

# Wiki: Algoritmi e Strutture Dati

Corso di Algoritmi e Strutture Dati — Università degli Studi di Brescia (Magistrale Informatica). Professori: Marina Zanella e Alessandro Saetti. Copre fondamenti teorici (calcolabilità, complessità), paradigmi di progetto (D&I, PD, greedy), strutture dati classiche e algoritmi su grafi.

## Articoli

### Fondamenti e analisi
- [[introduzione-algoritmi]] — Introduzione al corso: algoritmo, problema, modello RAM, analisi asintotica, paradigmi incrementale e divide-et-impera
- [[richiami-matematici]] — Notazioni asintotiche Θ, O, Ω, o, ω; proprietà; metodi per le ricorrenze; Master Theorem
- [[ricorrenze]] — Metodi per risolvere equazioni di ricorrenza: per ipotesi, sostituzioni successive, sostituzione (tentativo), Master Theorem con esempi
- [[calcolabilita]] — Macchina di Turing (MTD, MTM), T-decidibilità, T-semidecidibilità, Tesi di Church-Turing, problema della fermata, Teorema di Rice
- [[complessita-computazionale]] — Classi P, NP, co-NP, NP-completo, PSPACE; riducibilità polinomiale; SAT, 3-CNF-SAT, CLIQUE, TSP; approssimazione

### Algoritmi di ordinamento
- [[algoritmi-di-ordinamento]] — Heap binario, Heapsort O(n·lg n) in-loco, BUILD-HEAP O(n), coda con priorità; confronto Insertion/Merge/Heap
- [[quicksort]] — Partition di Hoare, analisi caso peggiore/medio/migliore, versione randomizzata, analisi probabilistica
- [[ordinamento-lineare]] — Limite inferiore Ω(n·lg n) per confronti (albero decisione); Counting-Sort, Radix-Sort, Bucket-Sort
- [[mediano-e-selezione]] — Statistiche d'ordine, MINIMUM O(n), min+max in 3⌈n/2⌉, RANDOMIZED-SELECT O(n) caso medio

### Strutture dati
- [[strutture-dati]] — Insiemi dinamici, pila (LIFO), coda (FIFO), liste concatenate semplici/doppie, sentinella
- [[alberi-binari-di-ricerca]] — Proprietà BST, visite (in-order, pre-order, post-order), ricerca, min/max, successore, inserimento, cancellazione O(h)
- [[tabelle-hash]] — Indirizzamento diretto, funzioni hash (divisione, moltiplicazione, universale), concatenazione Θ(1+α), indirizzamento aperto (lineare, quadratico, doppio hashing)

### Paradigmi di progetto
- [[programmazione-dinamica]] — Sottostruttura ottima, sottoproblemi sovrapposti, 4 fasi, bottom-up vs memoizzazione; Matrix-Chain-Order Θ(n³); LCS Θ(mn)

### Grafi e pathfinding
- [[grafi]] — Terminologia, rappresentazioni (liste/matrice adiacenza), BFS O(V+E) con distanze minime, DFS Θ(V+E) con classificazione archi, ordinamento topologico, SCC
- [[pathfinding]] — Gridmap, Dijkstra, Greedy BFS, A* con euristiche ammissibili (Manhattan, Chebyshev, Diagonale), JPS, CPD
