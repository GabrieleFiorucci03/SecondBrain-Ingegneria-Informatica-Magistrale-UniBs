---
tags: [ottimizzazione, TSP, branch-and-cut, max-flow, min-cut, ford-fulkerson, grafi]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/BranchCut_TSP_2021.pdf
---

# Branch-and-Cut per il TSP

Applicazione concreta dell'algoritmo [[exact-algorithms|Branch-and-Cut]] al Traveling Salesman Problem (TSP). Le connectivity constraints hanno numero esponenziale di vincoli, quindi vengono rimosse dalla formulazione e aggiunte dinamicamente solo se violate. Il separation problem per queste vincoli si riduce a un problema **Max-Flow / Min-Cut**, risolto con l'algoritmo di Ford-Fulkerson.

## Punti chiave

- Il TSP su grafo diretto G = (V, A) con n nodi richiede O(2ⁿ) connectivity constraints → si usano solo quando servono.
- Nel B&C per TSP, ad ogni nodo dell'albero: risolvi LP, verifica violazioni CC con Max-Flow, aggiungi tagli o fai branching.
- Il **separation problem** per le CC equivale a trovare la sezione di capacità minima in una rete con capacità xᵢⱼ*.
- L'algoritmo di **Ford-Fulkerson** trova il max flow (e quindi il min cut) usando cammini aumentanti nella rete incrementale.
- Il teorema **Max-Flow = Min-Cut** è il risultato teorico fondamentale che giustifica l'approccio.
- Per ogni sink t ∈ V \ {1} si risolve un problema Max-Flow separato; si prende la sezione con minima capacità tra tutte.
- La complessità dell'enumerazione completa (2ⁿ⁻¹ sottoinsiemi) è proibitiva per n = 50 → Ford-Fulkerson è essenziale.

## Formulazione Matematica del TSP

Dato G = (V, A) grafo completo orientato con costi cᵢⱼ:

```
min  Σ(i,j)∈A cᵢⱼxᵢⱼ                              (1)
     Σ(i,j)∈δ⁻(j) xᵢⱼ = 1       ∀j ∈ V             (2) in-degree = 1
     Σ(i,j)∈δ⁺(i) xᵢⱼ = 1       ∀i ∈ V             (3) out-degree = 1
     Σ(i,j)∈δ(S) xᵢⱼ ≥ 1        ∀S ⊂ V, 1 ∈ S      (4) connectivity
     xᵢⱼ ≥ 0, intero              ∀(i,j) ∈ A        (5)
```

Dove:
- δ(S) = {(i,j) ∈ A | i ∈ S, j ∉ S} = archi uscenti da S
- Il vincolo xᵢⱼ ≤ 1 è implicito da (2) e (3)
- I vincoli (2)-(3) da soli permettono soluzioni a più circuiti disgiunti; le CC (4) forzano il tour hamiltoniano unico

## Algoritmo B&C Base per TSP

Le O(2ⁿ) connectivity constraints vengono **rimosse** dal modello iniziale. Ad ogni nodo dell'albero B&C:

1. Risolvi la LP relaxation (senza CC);
2. Se il valore LP è peggiore dell'upper bound corrente → fathom;
3. Se tutte le variabili sono intere E non ci sono CC violate → soluzione intera ottima, fathom;
4. Se ci sono CC violate → aggiungile al modello, risolvi di nuovo (torna a 1);
5. Se nessuna CC violata ma soluzione frazionaria → branching su una variabile frazionaria (genera 2 sottoproblemi P1, P2).

## Separation Problem per le CC

**Problema**: dato x* ≥ 0, trovare S* ⊂ V con 1 ∈ S* tale che γ* := Σ(i,j)∈δ(S*) xᵢⱼ* sia minimale.

- Se γ* ≥ 1 → nessuna CC violata.
- Se γ* < 1 → il vincolo `Σ(i,j)∈δ(S*) xᵢⱼ ≥ 1` è quello più violato.

**Approccio naive** — enumerazione completa:
```
COMPLETEENUMERATIONALGORITHM(G, x*)
  S* ← ∅;  maxViolation ← 0
  for all S ⊂ V, 1 ∈ S do
    if 1 − Σ(i,j)∈δ(S) x*ᵢⱼ > maxViolation then
      maxViolation ← 1 − Σ(i,j)∈δ(S) x*ᵢⱼ
      S* ← S
  return S*
```
Complessità: 2ⁿ⁻¹ sottoinsiemi → per n=50 → **17851 anni** a 0.001 sec/subset!

## Max Flow come Separation Algorithm

**Insight chiave**: il separation problem per le CC è equivalente a un **Max-Flow / Min-Cut** problem.

**Costruzione**: crea rete G* = (V, A*) dove A* = {(i,j) ∈ A : xᵢⱼ* > 0}, con capacità kᵢⱼ = xᵢⱼ*. Sorgente fissa: s = 1.

Il sottoinsieme S* che si cerca corrisponde alla **sezione di capacità minima** (S*, V \ S*) nella rete G*.

**Procedura completa**: per ogni nodo t ∈ V \ {1}:
1. Risolvi Max-Flow da s=1 a t in G* con capacità xᵢⱼ*.
2. Ottieni la sezione minima (Sₜ*, V \ Sₜ*) per quel t.
3. Prendi la sezione con capacità minima tra tutte le t.

Se la capacità minima è < 1, aggiungi il relativo vincolo di connettività.

## Teoria del Max Flow

### Definizioni

**Flow Network**: grafo orientato G = (V, A) con capacità kᵢⱼ per ogni arco, sorgente s e pozzo t tali che δ⁻({s}) = δ⁺({t}) = 0.

**Feasible flow**: funzione x : A → ℝ tale che:
```
0 ≤ xᵢⱼ ≤ kᵢⱼ               ∀(i,j) ∈ A    (capacità)
Σⱼ:(i,j)∈A xᵢⱼ − Σⱼ:(j,i)∈A xⱼᵢ = 0    ∀i ∈ V \ {s,t}    (conservazione)
```

**Sezione (Cut)** (S, V\S): partizione dei nodi con s ∈ S e t ∈ V\S.

**Flusso attraverso la sezione**:
```
φ(S) := Σ(i,j)∈δ⁺(S) xᵢⱼ − Σ(i,j)∈δ⁻(S) xᵢⱼ
```
dove δ⁺(S) = archi uscenti da S, δ⁻(S) = archi entranti in S.

**Teorema 1** (Flusso costante): φ(S) = φ₀ per ogni sezione S (il flusso attraverso ogni sezione è uguale al valore del flusso φ₀).

**Teorema 2** (Upper Bound): φ(S) ≤ k(S) = Σ(i,j)∈δ⁺(S) kᵢⱼ (il flusso ≤ capacità della sezione).

### Teorema Max-Flow = Min-Cut

Un flusso x è ottimo per Max-Flow se e solo se esiste una sezione (S*, V\S*) con φ(S*) = k(S*) (sezione completamente satura). Questa sezione è quella di **capacità minima** nella rete.

**Formulazione LP del Max-Flow**:
```
max  φ₀
     Σⱼ:(i,j)∈A xᵢⱼ − Σⱼ:(j,i)∈A xⱼᵢ = φ₀    (i = s)
     Σⱼ:(i,j)∈A xᵢⱼ − Σⱼ:(j,i)∈A xⱼᵢ = 0     (i ∈ V \ {s,t})
     Σⱼ:(i,j)∈A xᵢⱼ − Σⱼ:(j,i)∈A xⱼᵢ = −φ₀   (i = t)
     xᵢⱼ ≤ kᵢⱼ,  xᵢⱼ ≥ 0
```
La matrice dei vincoli è **Totally Unimodular (TUM)** (matrice nodo-arco) → la LP relaxation ha soluzione intera se le capacità sono intere.

## Algoritmo di Ford-Fulkerson

Algoritmo greedy per Max-Flow basato su **rete incrementale** e **cammini aumentanti**.

**Definizioni**:
- **Rete incrementale** G̃ = (V, Ã): per ogni arco (i,j) ∈ A crea un arco diretto (i,j) con capacità residua k̃ᵢⱼ = kᵢⱼ − xᵢⱼ ≥ 0 e un arco inverso (j,i) con capacità k̃ⱼᵢ = xᵢⱼ ≥ 0. Rimuove archi con capacità residua = 0.
- **Cammino aumentante**: cammino da s a t in G̃ (archi diretti aumentano il flusso, archi inversi lo diminuiscono).

**Algoritmo**:
```
x := 0;  φ₀ := 0;  optimal := false
repeat
  costruisci rete incrementale G̃(V, Ã) da x
  cerca cammino P da s a t in G̃
  if P non esiste then
    optimal := true
  else
    δ := min{k̃ᵤᵥ : (u,v) ∈ P}
    φ₀ := φ₀ + δ
    for each (u,v) ∈ P do
      if (u,v) diretto:  xᵤᵥ := xᵤᵥ + δ
      else (arco inverso): xᵥᵤ := xᵥᵤ − δ
until optimal
```

**Teorema 3** (Ottimalità): un flusso x è ottimo ⟺ il nodo t non è raggiungibile da s nella rete incrementale G̃. La sezione di minima capacità è quella che separa nodi raggiungibili da s da quelli non raggiungibili.

## Esempio Ford-Fulkerson (Esercizio)

Rete con nodi {s, A, B, C, D, t}, capacità:
s→A=3, s→B=9, A→C=2, A→D=4, B→C=6, B→D=3, C→t=3, D→t=8.

| Step | Cammino aumentante | δ | φ₀ |
|------|-------------------|---|-----|
| 2 | s→A→C→t | 2 | 2 |
| 3 | s→B→C→t | 1 | 3 |
| 4 | s→B→D→t | 3 | 6 |
| 5 | s→B→C→A→D→t | 2 | 8 |
| 6 | s→A→D→t | 1 | 9 |

**Max flow = 9**. Sezione di minima capacità: S* = {s, B, C}, V\S* = {A, D, t}. Capacità = 3+3+3 = 9. ✓

## Applicazione Max-Flow al TSP (esempio)

Dato un LP frazionario del TSP (es. slide 55/60 con 6 nodi), si crea G* con capacità xᵢⱼ* e si risolve Max-Flow per ogni t ∈ {2,3,4,5,6}:

- t=2: max flow = 0.5 < 1 → S₂* = {1,3,4,5} → CC violata!
- t=3: max flow = 0.5 < 1 → S₃* = {1,2,6} → CC violata!
- t=4: max flow = 0.5 < 1 → S₄* = {1,2,6} → CC violata!

Si aggiunge il vincolo corrispondente alla sezione con violazione massima e si re-ottimizza.

## Articoli correlati

- [[exact-algorithms]]
- [[ottimizzazione-combinatoria]]
- [[traveling-salesman-problem]]
- [[approssimazione-algoritmi]]

## Fonti

- `raw/BranchCut_TSP_2021.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2021/2022.
