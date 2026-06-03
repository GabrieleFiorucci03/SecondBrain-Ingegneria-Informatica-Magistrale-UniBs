# Wiki: Algoritmi di Ottimizzazione

Wiki del corso universitario **Optimization Algorithms** (Prof. Renata Mansini, UniBS). Copre ottimizzazione combinatoria, algoritmi esatti (B&B, B&C), algoritmi di approssimazione, metaeuristiche (Tabu Search, GRASP, VNS, ALNS), matheuristiche (Kernel Search), il solver Gurobi, e problemi applicativi (TSP, ARP, MKP, Portfolio).

## Articoli

- [[ottimizzazione-combinatoria]] — Fondamenti di ottimizzazione combinatoria: formulazioni ILP/BIP/MILP, Convex Hull, confronto tra formulazioni (MST, Matching, Plant Location).
- [[exact-algorithms]] — Algoritmi esatti per ILP: Cutting Planes, B&B, B&C, valid inequalities, separation problem; esempio completo su Index Selection Problem.
- [[branch-and-cut-tsp]] — B&C applicato al TSP: connectivity constraints, separation via Max-Flow/Min-Cut, algoritmo Ford-Fulkerson.
- [[approssimazione-algoritmi]] — Algoritmi di approssimazione con bound garantiti: Tree Algorithm (TSP), LP Rounding (Vertex Cover), Next Fit (Bin Packing), Greedy (Knapsack).
- [[metaeuristiche-introduzione]] — Fondamenti di euristiche e local search: costruttive, first/best improvement, k-opt, introduzione alle metaeuristiche.
- [[tabu-search]] — Tabu Search: memoria adattiva (STM/LTM), tabu list, tabu tenure, aspiration criteria, intensificazione/diversificazione.
- [[grasp]] — GRASP: costruzione greedy randomizzata adattiva (RCL), local search, varianti parallele; esempio su Set Covering.
- [[vns]] — Variable Neighborhood Search: cambio sistematico di vicinato, VND, RVNS, VNDS, SVNS; applicazione al TSP.
- [[alns]] — Adaptive Large Neighborhood Search: destroy/repair, roulette wheel selection, Adaptive Weight Adjustment, Simulated Annealing acceptance.
- [[kernel-search]] — Kernel Search: framework matheuristico MIP; LP relaxation per Kernel Set, bucket construction, Fase 1/2, IKS.
- [[kernel-search-applicazioni]] — KS applicato a MKP (istanze Chu-Beasley, new best known) e Portfolio Selection (CVaR); drawbacks del framework.
- [[arc-routing-problems]] — Arc Routing Problems: CPP (polinomiale via Perfect Matching), URPP (NP-hard, algoritmo di Frederickson).
- [[gurobi]] — Gurobi solver: Java e Python API, parametri, attributi, callbacks, lazy constraints per TSP.
