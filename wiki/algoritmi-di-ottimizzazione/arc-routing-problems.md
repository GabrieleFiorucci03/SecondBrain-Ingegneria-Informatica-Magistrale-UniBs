---
tags: [ottimizzazione, arc-routing, CPP, RPP, grafi, euleriano, postino-cinese, routing]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/arp_2022_23.pdf
---

# Arc Routing Problems (ARP)

Classe di problemi di ottimizzazione in cui l'obiettivo è trovare un ciclo di costo minimo che attraversa un insieme di archi (o lati) almeno una volta. A differenza dei problemi di Node Routing (es. VRP, TSP), il "servizio" è richiesto sugli **archi/lati** del grafo. Applicazioni: raccolta rifiuti, distribuzione posta, spalatura neve, lettura contatori.

## Punti chiave

- **CPP** (Chinese Postman Problem): ciclo minimo che attraversa **tutti** i lati; polinomiale su grafi non orientati (si riduce a Perfect Matching).
- **RPP** (Rural Postman Problem): ciclo minimo che attraversa un **sottoinsieme** di lati richiesti R; NP-hard se il sottografo G_R non è connesso.
- Un grafo non orientato ha un ciclo euleriano ↔ tutti i nodi hanno grado pari.
- Il CPP non orientato si risolve con: shortest paths sugli odd-degree nodes + Perfect Matching + costruzione ciclo euleriano.
- L'RPP non orientato si riduce al CPP se G_R è connesso; altrimenti si usa l'euristica di Frederickson.
- La **gerarchia** di problemi ARP: CPP ⊂ RPP ⊂ GRP (generalità crescente).

## Classificazione dei problemi di routing

```
          Node Routing        Arc Routing         General Routing
          (VRP, TSP)         (CPP, RPP, GRP)    (nodi + archi)
```

**Gerarchia di generalità (asse verticale)**:
```
CPP  (tutti i lati, meno generale)
 ↓
RPP  (sottoinsieme di lati richiesti R)
 ↓
GRP  (sottoinsieme di lati E nodi, più generale)
```

**Tipo di grafo (asse orizzontale)**:
```
Undirected (lati) → Directed (archi) → Mixed (lati + archi) → Windy
```

| | Undirected | Directed | Mixed | Windy |
|---|---|---|---|---|
| CPP-tipo | CPP | DCPP | MCPP | WPP |
| RPP-tipo | RPP (=URPP) | DRPP | MRPP | WRPP |
| GRP-tipo | GRP | DGRP | MGRP | WGRP |

- **Grafi non orientati**: stesso costo in entrambe le direzioni.
- **Grafi diretti**: lati = archi, traversata solo in una direzione.
- **Grafi misti**: combinazione di lati e archi.
- **Grafi Windy**: grafo non orientato con costi diversi per le due direzioni; il più generale (ricreare tutti gli altri con costosi infiniti).

## Richiami: grafi Euleriani

**Grafo non orientato G = (V, E)**:
- **Hamiltoniano**: esiste un ciclo che visita ogni **nodo** esattamente una volta.
- **Euleriano**: esiste un ciclo che traversa ogni **lato** esattamente una volta.

**Teorema (Eulero, 1736)**: un grafo non orientato connesso è Euleriano ↔ tutti i nodi hanno grado pari.

**Grafi diretti**: un grafo orientato connesso è Euleriano ↔ in-degree(v) = out-degree(v) per ogni nodo v (grafo simmetrico).

## Chinese Postman Problem (CPP) — Grafo non orientato

**Definizione**: dato G = (V, E) non orientato, trovare il ciclo di costo minimo che attraversa ogni lato almeno una volta.

**Equivalenza**: trovare la **least-cost augmentation** di G che rende tutti i gradi pari (individuare i lati da attraversare più di una volta).

**Remark**: non è mai ottimale attraversare lo stesso lato più di due volte.

### Formulazione A (BIP)

xₑ = 1 se una copia del lato e è aggiunta all'augmentation.

```
min  Σₑ∈E cₑxₑ
     Σₑ∈δ(i) xₑ ≡ 1 (mod 2)    i ∈ S̄       [nodi a grado dispari]
     Σₑ∈δ(i) xₑ ≡ 0 (mod 2)    i ∈ V \ S̄   [nodi a grado pari]
     xₑ ∈ {0,1}                  e ∈ E
```

S̄ ⊆ V = insieme dei nodi a grado dispari.

**Soluzione**: Formulazione A si risolve come **Perfect Matching Problem** sul grafo Ḡ(S̄, E_S̄) con E_S̄ = {e=(i,j)∈E : i,j∈S̄, i<j}.

### Formulazione B (ILP — Edmonds & Johnson, 1973)

xₑ = numero di copie del lato e da aggiungere a G.

```
min  Σₑ∈E cₑxₑ
     Σₑ∈δ(S) xₑ ≥ 1    S ⊂ V, S odd   [odd-cut inequalities]
     xₑ ≥ 0,  intero     e ∈ E
```

S "odd" = S ⊂ V contiene un numero dispari di vertici a grado dispari.

**Proprietà**: i vincoli di taglio (odd-cut inequalities / blossom inequalities) garantiscono la parità dei vertici. La Formulazione B fornisce il Convex Hull delle soluzioni. Le blossom inequalities sono separabili in tempo polinomiale.

### Algoritmo polinomiale per CPP non orientato

1. Calcola il **percorso più breve** tra ogni coppia di nodi a grado dispari.
2. Costruisci un **grafo completo Ḡ** con i nodi a grado dispari e i costi = shortest paths.
3. Risolvi il **Perfect Matching Problem** su Ḡ (complessità O(|S̄|³)).
4. Aggiungi al grafo originale gli archi identificati per rendere tutti i gradi pari → grafo euleriano.
5. Usa Hierholzer [1873] o Fleury [1883] per trovare il **ciclo euleriano**.

**Esempio** (6 nodi, nodi a grado dispari: {1,3,4,6}):
- Shortest paths tra coppie: (1,3)=4, (1,4)=2, (1,6)=3, (3,4)=2, (3,6)=4, (4,6)=3
- Possible perfect matchings: {(1,3),(4,6)}=7, {(1,4),(3,6)}=6, {(1,6),(3,4)}=5
- Optimal perfect matching: {(1,6),(3,4)}=5
- Ciclo euleriano ottimo: 1→2→6→5→1→3→6→4→3→4→1 (cost = original + 5 = ?).

## Undirected Rural Postman Problem (URPP)

**Definizione**: generalizzazione del CPP non orientato su G = (V, E) dove solo un sottoinsieme **R ⊆ E** di lati deve essere attraversato.

- G è connesso, ma il **sottografo indotto G_R = (V_R, R)** non deve necessariamente esserlo.
- V_R ⊆ V = nodi incidenti ad archi in R.
- **Complessità**: NP-hard quando G_R non è connesso (riduzione dal TSP asimmetrico). Polinomiale se G_R è connesso (si riduce a CPP).

### Formulazione (Corberán & Sanchis, 1994)

xₑ = numero di copie del lato e da aggiungere a G_R.

```
min  Σₑ∈E cₑxₑ
     Σₑ∈δ(S) xₑ ≥ 2        S ⊂ V_R, δ_R(S) = ∅   [connectivity]
     Σₑ∈δ_R(i) xₑ ≡ |δ_R(i)| (mod 2)    i ∈ V_R   [parity]
     xₑ ≥ 0,  intero          e ∈ E
```

Dove:
- δ_R(S) = δ(S) ∩ R = archi in R che attraversano il taglio S
- Il primo set garantisce la **connettività** della soluzione
- Il secondo set garantisce la **parità dei gradi**

**Costruzione del grafo per la formulazione**:
1. Aggiungi a G_R = (V_R, R) un lato non richiesto tra ogni coppia di nodi V_R con costo = shortest path in G.
2. Per ogni coppia di lati paralleli con lo stesso costo, eliminane uno.
3. Rimuovi ogni lato e=(i,j) ∉ R con cᵢⱼ = cᵢₖ + cₖⱼ per qualche nodo k (lato dominato).

### Risoluzione

- **G_R connesso** → CPP su G_R → **polinomiale**.
- **G_R con p componenti connesse (R-components)** → NP-hard → metodi euristici.

## Algoritmo di Frederickson (euristica per URPP con G_R non connesso)

**Assunzione**: G non orientato con p componenti connesse disgiunte.

**Algoritmo**:
1. Costruisci un **grafo ausiliario H** con un nodo per ogni R-component. Il costo tra due nodi di H = shortest path in G tra le componenti.
2. Calcola il **Minimum Spanning Tree** di H → insieme T di archi.
3. Risolvi il **Perfect Matching** sui nodi a grado dispari del grafo indotto da E_R ∪ T. Siano M gli archi del matching.
4. Trova il **ciclo euleriano** nel grafo indotto da E_R ∪ T ∪ M.

**Nota**: i costi nei problemi di matching sono shortest paths in G.

**Esempio** (3 componenti: triangolo, griglia 3×3, griglia 4×4):
- H = grafo con 3 nodi, costi = shortest paths tra componenti
- MST di H seleziona 2 archi di connessione
- Matching sui nodi a grado dispari
- Ciclo euleriano costruito con i 5 path identificati nella tabella.

## Esempio URPP (grafo a 6 nodi, required edges = tratteggiati)

**G_R indotto**: connesso (verifica: il sottografo dei required edge è connesso → si riduce a CPP).

**Steps** (stesso algoritmo del CPP su G_R):
1. Nodi a grado dispari in G_R: {1, 2, 5, 6}.
2. Shortest paths: (1,2)=3, (1,5)=4, (1,6)=3, (2,5)=5, (2,6)=2, (5,6)=3.
3. Possible matchings: {(1,2),(5,6)}=6, {(1,5),(2,6)}=6, {(1,6),(2,5)}=8.
4. Optimal matchings (due a parità): {(1,2),(5,6)} e {(1,5),(2,6)} → entrambi cost=6.
5. Due cicli euleriani ottimi:
   - Soluzione 1: 1→2→6→5→6→3→1 (costo 6)
   - Soluzione 2: 1→3→6→2→6→5→1 (costo 6)

## Articoli correlati

- [[ottimizzazione-combinatoria]]
- [[exact-algorithms]]
- [[branch-and-cut-tsp]]
- [[approssimazione-algoritmi]]

## Fonti

- `raw/arp_2022_23.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2022/2023.
