---
tags: [ottimizzazione, exact-algorithms, branch-and-bound, branch-and-cut, cutting-planes, ILP]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/Exact Algorithms.pdf
---

# Algoritmi Esatti per ILP

Gli algoritmi esatti garantiscono di trovare la soluzione ottima di un problema ILP, ma possono richiedere un numero esponenziale di iterazioni nel caso peggiore. I principali approcci sono Cutting Planes, Branch-and-Bound e Branch-and-Cut, tutti basati sul concetto di **valid inequality** (taglio).

## Punti chiave

- **Algoritmi esatti**: trovano l'ottimo garantito; esempi: Cutting Planes, B&B, B&C, Dynamic Programming.
- **Algoritmi di approssimazione**: trovano soluzioni con gap garantito dalla ottima; tempo polinomiale.
- **Algoritmi euristici**: trovano buone soluzioni in tempo ragionevole, senza garanzia di qualità.
- Una **valid inequality** αᵀx ≤ α₀ è soddisfatta da tutte le soluzioni intere X = P ∩ Zⁿ ma violata dalla soluzione LP frazionaria x*.
- Il **separation problem**: dato x*, trovare un taglio che lo separa dall'insieme integero X.
- B&C è superiore al puro B&B (formulazione più forte) e al puro Cutting Planes (evita il *tailing-off*).
- I tagli devono essere **globalmente validi** lungo tutto l'albero decisionale.

## Valid Inequalities

Dato un ILP in forma standard:
```
min  cᵀx
Ax = b,  x ≥ 0,  x intero
```
Sia X = P ∩ Zⁿ l'insieme delle soluzioni intere e P = {x ≥ 0 : Ax = b} il poliedro.

**Definizione — Valid Inequality**: αᵀx ≤ α₀ tale che:
1. αᵀx ≤ α₀ per ogni x ∈ X (valida per tutte le soluzioni intere)
2. αᵀx* > α₀ (violata dalla soluzione LP corrente x*)

**Problema di separazione**: dato x*, trovare (se esiste) un taglio αᵀx ≤ α₀ violato da x*. Il taglio "taglia via" x* dal poliedro LP senza eliminare nessuna soluzione intera.

## Cutting Planes Method

**Algoritmo generico**:
1. Risolvi la LP relaxation → ottieni x*.
2. Se x* è intero → STOP, soluzione ottima trovata.
3. Altrimenti aggiungi una valid inequality soddisfatta da X ma violata da x*; torna a 1.

### Gomory's Cut
Genera tagli usando il tableau simplex ottimale. Data la riga t-esima del tableau (variabile in base con valore frazionario):
```
Σⱼ∈Φ φ(āₜⱼ)xⱼ ≥ φ(b̄ₜ)
```
dove φ(r) = r - ⌊r⌋ ≥ 0 è la parte frazionaria di r, e Φ è l'insieme degli indici delle variabili fuori base.

### Generic Cut
Se x* è frazionario, Φ = indici variabili fuori base, allora:
```
Σᵢ∈Φ xᵢ ≥ 1
```
è valida per ogni soluzione intera.

### Tagli specifici: Weighted Independent Set Problem
Dato G = (N, E) con pesi wᵢ, trovare il sottoinsieme S ⊆ N di peso massimo senza nodi adiacenti.

**Clique cut**: per ogni clique U ⊆ N: `Σᵢ∈U xᵢ ≤ 1`

**Cycle cut**: per ogni ciclo C di cardinalità dispari: `Σᵢ∈C xᵢ ≤ (|C|-1)/2`

## Branch-and-Bound (B&B)

Principio divide-et-impera: l'insieme F delle soluzioni viene partizionato in sottoproblemi F₁,...,Fₖ, ciascuno risolto separatamente.

**Algoritmo**:
1. Seleziona un sottoproblema attivo Fᵢ; se nessuno, STOP.
2. Se Fᵢ è inammissibile, eliminalo e vai a 1.
3. Se b(Fᵢ) ≥ U (**fathoming**), eliminalo e vai a 1.
4. Se Fᵢ ha soluzione intera ottima, aggiorna U e vai a 1.
5. Se b(Fᵢ) < U, **branching**: dividi Fᵢ in nuovi sottoproblemi; vai a 1.

Dove U = upper bound corrente (soluzione intera migliore nota), b(Fᵢ) = lower bound del sottoproblema.

**Elementi chiave**:
- **Upper bound U**: algoritmo euristico che trova una buona soluzione iniziale.
- **Lower bound b(Fᵢ)**: soluzione della LP relaxation del sottoproblema.
- **Branching**: scelta della variabile su cui ramificare (tipicamente variabile frazionaria).
- **Regola di selezione**: depth-first, breadth-first, best-bound.

**Esempio TSP**: lower bound = problema di assegnamento; branching = subtour elimination.

## Branch-and-Cut (B&C)

Combina B&B con generazione di tagli ad ogni nodo dell'albero decisionale.

**Idea**: ad ogni nodo t dell'albero, prima di fare branching, genera tagli per:
1. Rendere la soluzione LP x* intera (evitare il branching).
2. Rafforzare il lower bound.

**Algoritmo**:
1. Risolvi LP relaxation al nodo corrente → x*.
2. Se x* è frazionario: cerca nella *constraints pool* vincoli violati da x*; aggiungili e risolvi di nuovo.
3. Ripeti finché non si trovano più tagli violati.
4. Se x* ancora frazionario: attiva una procedura di separazione per nuove classi di tagli.
5. Se nessun taglio trovato: fai branching.

**Differenze rispetto ai metodi puri**:
- vs B&B: formulazione più forte → meno nodi esplorati.
- vs Cutting Planes: il branching evita il *tailing-off* (lenta convergenza nella fase finale).

**Elementi chiave**:
- **Constraints pool**: insieme di tagli globalmente validi accumulati durante l'esecuzione.
- **Separation procedures**: procedure (esatte o euristiche) che generano tagli violati dalla soluzione LP corrente.

**Design di un B&C**:
1. Identificare le caratteristiche strutturali del modello ILP.
2. Tradurre le proprietà strutturali in classi di valid inequalities (analisi poliedrale).
3. Per ogni classe C, sviluppare una procedura di separazione efficiente.

**Nota sulla convergenza**: la convergenza dipende dall'operazione di branching; le procedure di separazione possono essere euristiche o esatte.

## Esempio completo: Index Selection Problem (ISP)

**Descrizione**: progettare una base dati relazionale. Dati m query e n indici potenziali, scegliere quali indici installare (con budget di memoria D) per minimizzare i costi fissi degli indici + i costi per rispondere alle query.

**Dati**:
- cⱼ = costo fisso di installazione dell'indice j
- dⱼ = spazio occupato dall'indice j (in MB)
- γᵢⱼ = costo per rispondere alla query i usando l'indice j (γᵢ₀ = costo senza indici)
- D = spazio totale disponibile

**Variabili**:
- yⱼ ∈ {0,1}: 1 se l'indice j è selezionato
- xᵢⱼ ∈ {0,1}: 1 se la query i usa l'indice j

**Formulazione matematica**:
```
min  Σⱼ cⱼyⱼ + Σᵢ Σⱼ≥0 γᵢⱼxᵢⱼ           (1) obiettivo
     Σⱼ dⱼyⱼ ≤ D                          (2) memoria
     Σⱼ≥0 xᵢⱼ = 1      ∀i                 (3) un indice per query
     Σᵢ xᵢⱼ ≤ m·yⱼ     ∀j                 (4) coerenza x-y
     xᵢⱼ, yⱼ ∈ {0,1}
```

**Prima ottimizzazione**: per ogni indice j, sia Iⱼ = {i : γᵢⱼ < γᵢ₀} (query per cui j è più economico di 0). Le variabili xᵢⱼ con i ∉ Iⱼ possono essere fissate a 0. Il vincolo (4) diventa più stretto:
```
Σᵢ∈Iⱼ xᵢⱼ ≤ |Iⱼ|·yⱼ     ∀j              (7)
```

### Classe C₁ di valid inequalities

Osservazione: nella LP relaxation, se yⱼ* < 1 ma xᵢⱼ* = 1 per qualche i, abbiamo un'inconsistenza logica.

**Classe C₁**: `xᵢⱼ ≤ yⱼ   ∀j ∈ {1,...,n}, i ∈ Iⱼ`

**Problema di separazione per C₁**: dato (x*, y*), trovare la coppia (i, j) tale che xᵢⱼ* > yⱼ*.
```
for j := 1 to n do
  for each i ∈ Iⱼ do
    if x*ᵢⱼ > y*ⱼ then → vincolo xᵢⱼ ≤ yⱼ violato
```
Complessità: O(n·m) enumerazioni.

### Classe C₂ di valid inequalities

**Osservazione**: se un sottoinsieme S di indici supera la memoria D (Σⱼ∈S dⱼ > D), allora al massimo |S|-1 indici di S possono essere selezionati.

**Classe C₂**: `Σⱼ∈S yⱼ ≤ |S|-1   ∀S ⊆ {1,...,n}, S ≠ ∅ : Σⱼ∈S dⱼ > D`  (9)

**Problema di separazione per C₂** (formulato come ILP — Minimum Knapsack):
```
min  w := Σⱼ (1 - y*ⱼ)zⱼ
     Σⱼ dⱼzⱼ ≥ D + ε
     zⱼ ∈ {0,1}
```
Se w* < 1, il vincolo corrispondente alla soluzione ottima è violato.

**Procedura euristica per C₂** (limitare a sottoinsiemi di cardinalità ≤ k):
```
J* := {j : y*ⱼ > 0}
for each S ⊆ J*, 1 ≤ |S| ≤ k do
  if Σⱼ∈S dⱼ > D and Σⱼ∈S y*ⱼ > |S|-1 then → vincolo violato
```

## Articoli correlati

- [[ottimizzazione-combinatoria]]
- [[branch-and-cut-tsp]]
- [[approssimazione-algoritmi]]
- [[gurobi]]

## Fonti

- `raw/Exact Algorithms.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2021/2022.
