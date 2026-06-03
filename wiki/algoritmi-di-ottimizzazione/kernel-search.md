---
tags: [ottimizzazione, kernel-search, matheuristics, MIP, ILP, local-branching, RINS]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/OA_Kernel Search.pdf
---

# Kernel Search

Framework euristico per problemi MIP che sfrutta la **soluzione della LP relaxation** per identificare un insieme di variabili promettenti (Kernel Set), poi risolve una sequenza di problemi MIP ristretti su Kernel + bucket di variabili aggiuntive. Non richiede soluzione iniziale; classifica: **Matheuristica stand-alone per MIP generale**. Introdotta da Angelelli et al. (2010).

## Punti chiave

- Il **Kernel Set Λ** contiene le variabili più promettenti (con valore LP positivo e/o costo ridotto vicino a zero).
- Si risolve una sequenza di **MIP ristretti**: MIP(Λ ∪ Bᵢ), dove Bᵢ è il bucket corrente.
- La Fase 1 (inizializzazione) costruisce il Kernel Set e la soluzione iniziale; la Fase 2 (miglioramento) scorre i bucket e aggiorna Λ.
- Due vincoli aggiunti per efficienza: **cut-off** (obj ≥ w*) e **bucket selection** (Σⱼ∈Bᵢ xⱼ ≥ 1).
- Il Kernel Set cresce **monotonicamentte** (nessuna variabile viene rimossa).
- **IKS** (Iterative Kernel Search) scorre i bucket più volte finché Λ non smette di crescere.
- Non richiede soluzione iniziale; stand-alone; applicabile a MIP generale (non solo BIP).

## Contesto: Matheuristiche

Le **matheuristiche** combinano metodi esatti di programmazione matematica con metaeuristiche.

### Metodi generali noti (confronto con KS)

| | LB | RINS | PS | **KS** |
|---|---|---|---|---|
| Problema | BIP | MIP | BIP | **MIP** |
| Algoritmo esatto | sì | no | no | no |
| Euristica primale | sì | sì | sì | sì |
| Stand-alone | no | no | sì | **sì** |
| Soluzione iniziale richiesta | sì | sì | sì | **no** |

- **Local Branching** (Fischetti & Lodi, 2003): neighborhood k-opt, embedded in B&B, richiede soluzione iniziale.
- **RINS** (Danna et al., 2005): fissa le variabili con stesso valore in incumbente e LP relaxation; richiede soluzione iniziale.
- **Proximity Search** (Fischetti & Salvagnin, 2014): cut-off constraint + proximity function; richiede soluzione iniziale.

## Considerazioni preliminari

Osservazioni empiriche sui problemi MIP:
- Spesso in una soluzione ottima ci sono **poche variabili non-zero**.
- Le **variabili di base** nella LP relaxation sono buoni predittori delle variabili non-zero nella soluzione MIP ottima.
- I **costi ridotti** (reduced costs) delle variabili fuori base predicono la probabilità che una variabile sia zero o non-zero nella soluzione MIP ottima.

## Idea principale

```
Identificare un KERNEL SET Λ
         ↓
insieme di variabili PROMETTENTI
(probabilmente non-zero nella soluzione ottima)
         ↓
Costruire una sequenza di problemi MIP RISTRETTI
(includono Kernel Set + alcune variabili aggiuntive)
```

**Dimensione del problema ristretto**:
- Non troppo grande: evitare eccessivo costo computazionale.
- Non troppo piccola: permettere la selezione congiunta di variabili correlate.

## Struttura del framework

```
┌─────────────────────────────────┐
│         Strategic Level          │
│      Heuristic Framework         │
│    (progettato dall'utente)      │
│              ↑↓                  │
├─────────────────────────────────┤
│         Operational Level        │
│       Generic MIP Solver         │
│     (CPLEX / GUROBI)             │
└─────────────────────────────────┘
```

**Caratteristiche**:
- Semplice da implementare
- Il lavoro pesante è in carico al MIP solver
- Trade-off qualità/efficienza controllato da pochi parametri

## Phase 1 — Initialization

**Steps**:
1. Risolvi la **LP relaxation** del problema originale
2. Ordina le variabili (**Sorting Criterion**)
3. Costruisci il **Initial Kernel Set Λ**
4. Partiziona le variabili rimanenti in una sequenza di bucket `{Bᵢ | i=1,...,nb}`
5. Risolvi il problema ristretto **MIP(Λ)** → soluzione x* con valore w*

### LP Solution

```
max  cᵀx
     Ax = b,  x ≥ 0
```

Soluzione ottima: x = (x_B, x_N) dove:
- x_B = B⁻¹b (variabili di base; valore dalla base ottimale)
- Reduced costs (variabili fuori base): c_N' = c_Nᵀ − c_Bᵀ B⁻¹ N

Per il Bounded Simplex (0 ≤ x ≤ 1): le variabili fuori base si dividono tra x=0 (RC≤0) e x=1 (RC≥0).

**Valore obiettivo LP** (formula per sorting):
```
cᵀx = c_Bᵀ(B⁻¹b − B⁻¹Nx_N) + c_Nᵀx_N
     = c_Bᵀ B⁻¹ b + (c_Nᵀ − c_Bᵀ B⁻¹ N) x_N
```

### Sorting Criterion

Ordina le variabili secondo:
1. **Valore LP decrescente** (variabili di base prima, con valore ≥0, quelle maggiori prima).
2. **Valore assoluto del reduced cost crescente** (variabili fuori base: quelle più economicamente "vicine" all'ottimo vengono prima).

Risultato visivo: le variabili sono ordinate da sinistra (più promettenti) a destra (meno promettenti).

### Kernel Set Construction

Seleziona le prime **C variabili** (dove C = numero di variabili con valore LP positivo).

Corrisponde alle variabili di base con valore maggiore di zero nella soluzione LP.

### Initial Solution

Risolvi il problema ristretto MIP(Λ) con CPLEX/Gurobi:
- Ottieni soluzione ottima x* e valore ottimo w*.
- Se nessuna soluzione trovata: w* := −∞.

### Buckets Construction

Partiziona le variabili rimanenti (dopo Λ) in bucket B₁, B₂, ..., Bnb.

**Parametri di design**:

| Parametro | Opzioni | Trade-off |
|----------|---------|-----------|
| Cardinalità bucket | SMALL / LARGE | Small: nb↑, efficienza↓; Large: nb↓, efficacia↑ |
| Numero bucket | FEW / MANY | Few: ordine rispettato; Many: ordine perso |
| Dimensione | FIXED / VARIABLE | Fixed: kC; Variable: decreasing/increasing |
| Sovrapposizione | DISJOINT / OVERLAPPING | Overlapping: affinità tra variabili correlate |

**Fixed size**: `|Bᵢ| = kC` per qualche k > 0.

**Variable size (linearly decreasing)**: `|B₁| = C`, `|Bᵢ| = max{s₁, C − s₂(i−1)}` per i=2,...,nb.

**Overlapping (shifted buckets)**: `|B₁| = 1.5C`, `|Bᵢ| = C` per i=2,...,nb. Bucket traslati → affinità tra variabili adiacenti. Raccomandati.

## Phase 2 — Improvement

**Steps**:
1. Imposta i := 1
2. Costruisci il set Λ ∪ Bᵢ (Bᵢ = bucket corrente)
3. Risolvi il problema ristretto MIP(Λ ∪ Bᵢ) con due vincoli aggiuntivi:
   - **First constraint**: `valore obiettivo ≥ w*` (cut-off: non accettiamo soluzioni peggiori)
   - **Second constraint**: `Σⱼ∈Bᵢ xⱼ ≥ 1` (almeno una variabile del bucket deve essere selezionata)
4. Se MIP(Λ ∪ Bᵢ) è ammissibile:
   - Aggiorna soluzione incumbente: (x*, w*)
   - Costruisci Λ̄ᵢ := {variabili selezionate in Bᵢ}
   - Aggiorna Kernel Set: Λ := Λ ∪ Λ̄ᵢ
5. Se i < nb: imposta i := i+1 e vai a Step 2; altrimenti **STOP**

**Dual purpose** della soluzione di ogni MIP(Λ ∪ Bᵢ):
1. Trovare una soluzione ammissibile migliorante.
2. Identificare nuove variabili da aggiungere al Kernel Set.

### Scrolling visuale

```
Iter 1: [Λ][B₁] → risolvi → Λ cresce con variabili selezionate da B₁
Iter 2: [Λ'][B₂] → risolvi → Λ cresce con variabili selezionate da B₂
Iter 3: [Λ''][B₃] → risolvi → Λ cresce con variabili selezionate da B₃
```

## IKS — Iterative Kernel Search

Variante in cui i bucket vengono scorsi **più di una volta**.

**Algoritmo** (Repeat until no more variables added to Kernel Set):
1. Chiama PHASE 1 + PHASE 2
2. Imposta nb := q − 1 dove q := max{i | Λ̄ᵢ ≠ ∅}
3. Chiama PHASE 2
4. Se una nuova variabile è entrata nel Kernel Set:
   - riporta nb al valore iniziale
   - Chiama PHASE 2 e vai a Step 2

**Esempio** (3 iterazioni):
- Iter 1: nb=4, scorre B₁,...,B₄ → alcune variabili entrano in Λ (dal bucket B₂)
- Iter 2: nb=2 (si riparte dal bucket successivo), nuove variabili entrano
- Iter 3: STOP (nb=4, nessuna nuova variabile)

## Aspetto critico

Il Kernel Set cresce **monotonicamentte**: nessuna variabile viene rimossa. Questa è una limitazione → varianti avanzate possono prevedere la rimozione di variabili "non più promettenti".

## Esempio: MMKP (Multidimensional Multiple-Choice Knapsack)

**Problema**: n gruppi Gᵢ, nᵢ item per gruppo, m risorse, profitto pᵢⱼ, peso wᵢⱼᵏ, disponibilità cₖ per risorsa k. Scegliere un item per gruppo massimizzando il profitto senza eccedere le risorse.

```
max  ΣᵢΣⱼ pᵢⱼxᵢⱼ
     ΣᵢΣⱼ wᵢⱼᵏxᵢⱼ ≤ cₖ    k=1,...,m
     Σⱼ xᵢⱼ = 1              i=1,...,n
     xᵢⱼ ∈ {0,1}
```

**Sorting**: si calcolano xᵢⱼLP e RC per ogni item. Ordine: prima le variabili con xLP > 0 (decrescente), poi le variabili con xLP = 0 ordinate per |RC| crescente.

**Kernel Set**: variabili selezionate nella LP relaxation (quelle con xLP > 0). I loro indici formano Λ.

## Riepilogo

- LP relaxation → Kernel Set e bucket
- Costruzione e soluzione di una sequenza di MIP ristretti di piccola/media dimensione
- Miglioramento successivo del bound
- Kernel Set cresce monotonicamentte (nessun item scaricato)

## Articoli correlati

- [[exact-algorithms]]
- [[ottimizzazione-combinatoria]]
- [[gurobi]]
- [[alns]]
- [[kernel-search-applicazioni]]

## Fonti

- `raw/OA_Kernel Search.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2024/2025.
