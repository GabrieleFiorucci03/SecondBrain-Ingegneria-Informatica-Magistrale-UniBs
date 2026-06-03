---
tags: [ottimizzazione, metaeuristica, alns, lns, local-search, vrp, destroy-repair]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/ALNS.pdf
---

# Adaptive Large Neighborhood Search (ALNS)

Metaeuristica basata sul concetto di **Large Neighborhood Search (LNS)**: alterna un'**euristica di rimozione** (destroy) che elimina q componenti dalla soluzione e un'**euristica di inserimento** (repair) che la rende nuovamente ammissibile. La variante **adattiva** (ALNS) usa un set di euristiche di rimozione e inserimento, selezionando ad ogni iterazione la coppia (d, r) con un meccanismo a **roulette wheel** i cui pesi vengono aggiornati adattativamente.

## Punti chiave

- LNS genera vicinati **molto grandi** (esponenziali) tramite mosse distruttive; neighborhood implicita.
- Il parametro **q** (grado di distruzione) bilancia diversificazione (q alto) e intensificazione locale (q basso).
- ALNS introduce **più euristiche** di rimozione e inserimento e le seleziona con **roulette wheel**.
- I pesi delle euristiche si aggiornano con **Adaptive Weight Adjustment** (AWA) in segmenti: ρⱼ = λρⱼ + (1-λ)Ψ.
- L'accettazione della nuova soluzione usa **Simulated Annealing**: accetta soluzioni peggioranti con probabilità decrescente.
- LNS interpretato come **Fix-Optimize**: fix = removal (fissa componenti non rimossi), optimize = insertion (risolve il sottoproblema).

## Background: Very Large-Scale Neighborhood

| Approccio tradizionale | Very Large-Scale Neighborhood |
|---|---|
| Mosse semplici, non distruttive (es. 2-opt TSP) | Mosse "grandi", molto distruttive |
| Soluzioni molto simili | Diversificazione 30-40% in una singola iterazione |
| Vicinati piccoli, costo computazionale basso | Vicinati potenzialmente più ricchi di buone soluzioni |
| | Costo computazionale più alto |

## Large Neighborhood Search (LNS)

Sviluppato da Shaw (1997) per Vehicle Routing Problems.

**Features**:
- **Removal heuristic (destroy method)**: data una soluzione ammissibile e q, produce una nuova soluzione in cui q componenti sono stati rimossi.
- **Insertion heuristic (repair method)**: inserisce q componenti nella soluzione parziale, generando una nuova soluzione ammissibile.
- Il vicinato è **implicitamente definito** dalla combinazione delle due euristiche.

**Pseudocodice (Algorithm 1 — LNS)**:
```
input: feasible solution x
xᵇ ← x
repeat
  xᵗ ← r(d(x))           // applica destroy poi repair
  if accept(xᵗ, x):
    x ← xᵗ
  if c(xᵗ) < c(xᵇ):
    xᵇ ← xᵗ
until stop criterion is met
return xᵇ
```

### Il parametro q

- q = dimensione del vicinato = numero di componenti da rimuovere (grado di distruzione).
- q ∈ {0,...,n} dove n = numero di componenti della soluzione.
  - q=0: nessuna mossa applicata.
  - q=n: il problema originale è risolto da zero.
- q basso → ottimi locali (fine-tuning); q alto → re-ottimizzazione globale.

**Come gestire q**:
- Incremento/decremento graduale durante la ricerca.
- Selezione random in un range predefinito ad ogni iterazione.

### Fattibilità delle soluzioni

LNS alterna soluzioni (potenzialmente) inammissibili e ammissibili:
- La removal heuristic può generare soluzioni inammissibili.
- La insertion heuristic le rende ammissibili.
- La fattibilità **non è un requisito forte**: una soluzione inammissibile può essere scartata o penalizzata nella funzione obiettivo.

### LNS come Fix-Optimize

- **Fix**: una parte dei componenti della soluzione è fissata al valore corrente (componenti non rimossi dalla removal heuristic).
- **Optimize**: il problema viene re-ottimizzato a partire dalla soluzione corrente, tenendo conto dei componenti fissati.

## Da LNS ad ALNS

LNS usa una sola coppia (removal, insertion). ALNS introduce:
- Un **set** di euristiche di rimozione Ω⁻ = {d₁,...,dₖ₋}.
- Un **set** di euristiche di inserimento Ω⁺ = {r₁,...,rₖ₊}.
- Un **criterio di selezione** e un **sistema di valutazione** per scegliere la coppia (d, r) ad ogni iterazione.

**Pseudocodice (Algorithm 2 — ALNS)**:
```
input: feasible solution x
xᵇ ← x;  ρ⁻ ← (1,...,1);  ρ⁺ ← (1,...,1)
repeat
  seleziona destroy d ∈ Ω⁻ e repair r ∈ Ω⁺ usando ρ⁻ e ρ⁺
  xᵗ ← r(d(x))
  if accept(xᵗ, x):
    x ← xᵗ
  if c(xᵗ) < c(xᵇ):
    xᵇ ← xᵗ
  aggiorna ρ⁻ e ρ⁺
until stop criterion is met
return xᵇ
```

## Euristiche di rimozione

### Shaw Removal

Un componente casuale viene rimosso, poi si calcola la **relatedness measure** (similarità con i componenti rimasti). I q-1 componenti con la più alta relazionalità vengono rimossi.

**Esempio VRP**: `R(i,j) = 1 / (cᵢⱼ + Vᵢⱼ)` dove cᵢⱼ = costo normalizzato i→j, Vᵢⱼ = 1 se i e j sono serviti da veicoli diversi. Alta relazionalità → probabilmente assegnati allo stesso veicolo.

### Worst Removal

I q componenti **peggiori in termini di costo** vengono rimossi.

**Procedura**: data soluzione x, f'(x,i) = valore se si rimuove il componente i.
1. Lista componenti ordinata per f' crescente.
2. Partendo dalla testa, rimuovi con probabilità p. Fermati quando q componenti sono stati rimossi.
- p alto → rimozione quasi deterministica dei peggiori.
- p basso → possibilità di rimuovere qualche componente buono (diversificazione).

### Random Removal

q componenti rimossi casualmente. Massima diversificazione.

## Euristiche di inserimento

### Basic Greedy

Inserisce i q componenti che producono gli **incrementi di costo minimi**.

### Regret (per problemi di routing)

Considera la differenza di costo tra inserire un nodo nel miglior percorso e nel k-esimo miglior percorso.

Siano Δfᵢᵏ il costo di inserimento del nodo i nel suo k-esimo miglior percorso:
```
Regret-2:  j ← argmaxᵢ (Δfᵢ² − Δfᵢ¹)
Regret-k:  j ← argmaxᵢ Σₕ₌₂ᵏ (Δfᵢʰ − Δfᵢ¹)
```
Il nodo j viene inserito nel suo miglior percorso, il processo si ripete per q nodi.

### Exact Insertion

- I componenti non rimossi dalla removal heuristic sono **fissati** al loro valore corrente.
- Un **MILP solver** (es. [[gurobi]]) risolve il sottoproblema generato.
- Possibili vincoli aggiuntivi: upper/lower bound sul numero di modifiche rispetto alla soluzione corrente.

## Selezione: Roulette Wheel

**Motivazione**: alternare euristiche diverse rende il metodo più robusto.

Ad ogni euristica j viene assegnato un peso ρⱼ⁻ (o ρⱼ⁺). L'euristica j viene selezionata con probabilità:
```
P(j) = ρⱼ⁻ / Σᵢρᵢ⁻
```
La rimozione e l'inserimento sono selezionati **indipendentemente**.

## Aggiornamento pesi: Adaptive Weight Adjustment (AWA)

**Idea base**:
1. La ricerca è divisa in **segmenti** (es. 100 iterazioni).
2. All'inizio di ogni segmento, i pesi vengono riportati al **valore di default (1)**.
3. Ad ogni iterazione in cui un'euristica è selezionata, il suo peso viene aggiornato in base alla qualità della soluzione ottenuta.

**Score Ψ** assegnato alla coppia (d, r) in base al risultato:
- ω₁ se xᵗ è un **ottimo globale** (c(xᵗ) < c(xᵇ))
- ω₂ se xᵗ è **migliore della corrente** (c(xᵗ) < c(x))
- ω₃ se xᵗ è **peggiore ma accettata** (c(xᵗ) ≥ c(x) e accept = true)
- ω₄ se xᵗ è **peggiore e rifiutata** (c(xᵗ) ≥ c(x) e accept = false)

Tipicamente ω₁ > ω₂ > ω₃ > ω₄.

**Formula di aggiornamento** (dopo ogni iterazione in cui j è selezionata):
```
ρⱼ⁻ ← λ·ρⱼ⁻ + (1−λ)·Ψ
```
λ ∈ [0,1] = **decay parameter** (quanto pesare la storia recente vs il passato).

**Considerazioni avanzate**:
- Pesi aggiornabili in base alla **magnitudo** del miglioramento/peggioramento.
- Pesi aggiornabili in base al **tempo** richiesto dall'euristica (trade-off velocità/qualità).
- Pesi aggiornabili in base al **numero di iterazioni** dall'ultimo miglioramento.
- Cambio della strategia di aggiornamento in base alla fase (intensificazione/diversificazione).

## Acceptance Criterion: Simulated Annealing

**SA acceptance**: data soluzione corrente x, soluzione ottenuta xᵗ:
```
accept(xᵗ, x) = true  con probabilità  e^{−(c(xᵗ) − c(x)) / Tₙ}
```
- Se c(xᵗ) < c(x): accetta sempre (esponenziale > 1 → troncato a 1).
- Se c(xᵗ) ≥ c(x): accetta con probabilità decrescente.

**Cooling schedule**:
```
Tₙ = α·Tₙ₋₁ = αⁿ·T₀
```
- T₀ = temperatura iniziale alta.
- α ∈ [0,1] = cooling rate (determina la velocità di diminuzione).
- La temperatura può essere ridotta ogni m iterazioni.
- La temperatura può essere riportata al valore iniziale se l'algoritmo è bloccato in un ottimo locale.

## Termination Criteria

- Max numero di iterazioni.
- Max numero di iterazioni dall'ultimo miglioramento (di xᵇ o x).
- Max computing time.

## Articoli correlati

- [[vns]]
- [[tabu-search]]
- [[grasp]]
- [[metaeuristiche-introduzione]]
- [[gurobi]]

## Fonti

- `raw/ALNS.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS (Academic year non specificato).
