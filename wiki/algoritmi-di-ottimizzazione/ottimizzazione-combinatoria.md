---
tags: [ottimizzazione, combinatoria, ILP, MILP, BIP, programmazione-lineare-intera]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/Combinatorial Optimization.pdf
---

# Ottimizzazione Combinatoria

Branca dell'ottimizzazione matematica che cerca un oggetto ottimo (sottoinsieme, percorso, permutazione) selezionandolo da un insieme finito di oggetti discreti. Formalizzata come problema di Programmazione Lineare Intera (ILP/MILP/BIP). La qualità della formulazione matematica è determinante per l'efficienza degli algoritmi esatti.

## Punti chiave

- Un **COP** (Combinatorial Optimization Problem) cerca il sottoinsieme S ⊆ N di peso minimo tra tutte le soluzioni ammissibili F: `min { Σ cⱼ : S ∈ F }`.
- Un COP può sempre essere formulato come BIP, ILP o MILP.
- La **qualità di una formulazione ILP** si misura dalla vicinanza del poliedro LP al Convex Hull CH(X) delle soluzioni intere: più piccolo il poliedro LP, più forte la formulazione.
- Formulazione A è **almeno altrettanto forte** di B se P_A ⊆ P_B (regione ammissibile LP di A contenuta in quella di B).
- Il **Convex Hull CH(X)** è la formulazione ideale: risolvere il suo LP dà direttamente la soluzione intera ottima, ma computarlo è in generale NP-hard.
- Formulazioni più forti hanno bound LP più stretti → i metodi esatti (B&B, B&C) convergono più velocemente.
- Formulations con numero esponenziale di vincoli possono essere più forti (es. MST subtour elimination).

## Problema di ottimizzazione generale

```
min  f(x)
s.t. gᵢ(x) ≥ 0    i = 1,...,m
     hⱼ(x) = 0    j = 1,...,p
     x ≥ 0
```

**MILP** (Mixed Integer Linear Programming):
```
min  cᵀx + dᵀy
     Ax + By ≥ b
     x, y ≥ 0
     x intero
```
- Se tutto x è intero → **ILP**
- Se tutto x è binario → **BIP**

## Esempi canonici di COP

### 0-1 Knapsack Problem
- N = {1,...,n} oggetti, peso wᵢ, valore vᵢ, capacità zaino b.
- `max { Σᵢ∈I vᵢ : Σᵢ∈I wᵢ ≤ b }`, soluzione = sottoinsieme I ⊆ N.

### Set Covering Problem
- M = {1,...,m} regioni, N = {1,...,n} facility con costo cⱼ, Sⱼ ⊆ M regioni coperte da j.
- `min { ΣⱼT cⱼ : ∪ⱼ∈T Sⱼ = M }`, soluzione = collezione T di facility.

### Traveling Salesman Problem (TSP)
- N = {1,...,n} città, costo cᵢⱼ per l'arco (i,j).
- `min { Σⱼ c_{π,π(j)} : π ∈ T }` dove T = permutazioni cicliche.
- Soluzione = permutazione ciclica {π₁,...,πₙ}.

## Confronto tra formulazioni: Minimum Spanning Tree

**MST Problem**: grafo G = (N, E), trovare l'albero ricoprente di costo minimo.

**Formulazione A (subtour elimination)**:
```
min  Σ cₑxₑ
     Σₑ xₑ = n-1
     Σₑ∈E(S) xₑ ≤ |S|-1    S ⊂ N, S ≠ ∅
     xₑ ∈ {0,1}
```
E(S) = archi interni al sottoinsieme S. Numero esponenziale di vincoli.

**Formulazione B (cutset)**:
```
min  Σ cₑxₑ
     Σₑ xₑ = n-1
     Σₑ∈δ(S) xₑ ≥ 1    S ⊂ N, S ≠ ∅
     xₑ ∈ {0,1}
```
δ(S) = archi che attraversano il taglio (S, N\S). Anch'essa ha numero esponenziale di vincoli.

**Risultato**: P_A ⊂ P_B, cioè la formulazione A è **più forte**. Inoltre P_A = CH(X): la relaxation LP di A è esatta per MST.

## Confronto tra formulazioni: 0-1 Plant Location

**Formulazione A**: vincolo `Σᵢ xᵢⱼ ≤ m·yⱼ` (un'apertura copre al massimo m clienti).  
**Formulazione B**: vincolo più stretto `xᵢⱼ ≤ yⱼ ∀i,j`.

Le due formulazioni danno la stessa soluzione intera ottima, ma P_PLB ⊂ P_PLA → B è più forte. Il bound LP di B è almeno pari a quello di A: Z_PLA ≤ Z_PLB ≤ Z*.

## Perfect Matching Problem

Dato un grafo G = (N, E) con n pari, trovare un matching di costo minimo che abbini tutti i nodi.

**Formulazione A**: `Σₑ∈δ({i}) xₑ = 1 ∀i`, xₑ ∈ {0,1}.

**Formulazione B**: aggiunge vincoli di parità `Σₑ∈δ(S) xₑ ≥ 1` per ogni S ⊂ N con |S| dispari.

**Risultato**: P_B = CH(M), ovvero la formulazione B è esatta (Convex Hull del matching). P_A ≠ CH(M).

## Qualità di una formulazione

La **qualità** è giudicata dalla vicinanza del poliedro LP al CH(X):
- Z* = valore ottimo intero
- Z_LP = valore ottimo della relaxation continua
- Gap = Z* - Z_LP (gap di integralità)

Formulazione più forte → gap minore → bound LP più vicino a Z* → [[branch-and-bound]] e [[branch-and-cut]] convergono in meno nodi.

## Articoli correlati

- [[exact-algorithms]]
- [[branch-and-bound]]
- [[branch-and-cut]]
- [[traveling-salesman-problem]]
- [[programmazione-lineare]]

## Fonti

- `raw/Combinatorial Optimization.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2025/2026.
