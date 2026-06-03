---
tags: [ottimizzazione, kernel-search, matheuristics, MKP, portfolio, CVaR, applicazioni]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/OA_Kernel Search - applicazioni.pdf
---

# Kernel Search: Applicazioni

Due applicazioni principali del framework [[kernel-search]] a problemi reali: il **Multidimensional Knapsack Problem (MKP)** (che ha portato all'introduzione del concetto di bucket iterativo) e il **Portfolio Selection Problem con CVaR** (problema misto-intero molto complesso). Entrambe le applicazioni confermano che i reduced costs LP sono ottimi predittori delle variabili promettenti.

## Punti chiave

- MKP: fixed-length disjoint buckets + IKS; risultati migliorano best known su istanze Chu-Beasley.
- Portfolio: variabili x (continue) e z (binarie) separate; sorting su |rⱼ|/lⱼ o |rⱼ| dei reduced costs.
- Bucket grandi → qualità migliore ma tempi più alti; trade-off guidato da soglie temporali.
- Su MKP: bucket piccolo (k=0.1) spesso migliore di bucket grande (k=1) a parità di tempo limite 1h.
- Su Portfolio: IKS con buon sorting equivale in qualità al Basic KS; sorting B (|rⱼ|) è il migliore.
- **Drawbacks di KS**: segnale debole dei reduced costs; Kernel Set che cresce troppo; correlazione tra variabili; controllo dinamico del tempo.

## Applicazione 1: Multidimensional Knapsack Problem (MKP)

### Formulazione

```
max  Σⱼ₌₁ⁿ pⱼxⱼ                        (1) profitto
     Σⱼ₌₁ⁿ wᵢⱼxⱼ ≤ cᵢ    i=1,...,m    (2) vincoli di capacità
     xⱼ ∈ {0,1}            j=1,...,n   (3) binario
```

- Problema binario puro (una sola famiglia di variabili)
- NP-hard (fortemente)
- Benchmark standard: istanze di Chu-Beasley

### KS settings per MKP

- **Sorting criterion**: valore LP + reduced costs (standard KS)
- **Kernel Set iniziale**: C = numero di variabili selezionate nella LP relaxation
- **Buckets**: solo fixed length **disjoint** (k = 1, 0.2, 0.1)
- **Variante**: solo **IKS** (iterativa)
- **Time threshold**: limite di tempo fisso per ogni MILP ristretto

### Risultati computazionali

**Setup**: CPLEX 9.0, Pentium 3GHz, RAM 2GB. Time limit: 1 ora.

**Istanze**: 270 istanze Chu-Beasley con:
- n = 100, 250, 500 items; m = 5, 10, 30 risorse
- 30 istanze per ogni coppia (n, m)
- wᵢⱼ ∈ U(0,1000), tightness ratio α = 0.25, 0.50, 0.75
- pⱼ = α·Σᵢ(wᵢⱼ/m) + 500qⱼ dove qⱼ ∈ U(0,1)

**Risultati** (% errore medio rispetto ai Best Known values):

| n | m | FB-I(1) | FB-I(0.2) | FB-I(0.1) |
|---|---|---------|-----------|-----------|
| 100 | 5 | **0.000%** | 0.024% | 0.024% |
| 100 | 10 | -0.001% | 0.066% | 0.047% |
| 100 | 30 | **-0.018%** | 0.022% | 0.006% |
| 250 | 5 | -0.006% | 0.003% | **-0.003%** |
| 250 | 10 | -0.025% | **-0.017%** | -0.022% |
| 250 | 30 | **-0.038%** | -0.026% | -0.026% |
| 500 | 5 | 0.002% | 0.004% | 0.003% |
| 500 | 10 | 0.014% | 0.015% | 0.016% |
| 500 | 30 | 0.062% | 0.047% | 0.062% |

*Valori negativi = soluzione migliore del best known al momento.*

**New Best Known values** (miglioramenti rispetto alla letteratura):
- m=5: 30/100 ottimi, 11/250 migliorati, 1/500 migliorato
- m=10: 1/100, 20/250, 2/500 migliorati
- m=30: 6/100, 23/250, 1/500 migliorati

### Lezioni da MKP

1. I **reduced costs** forniscono un segnale buono per identificare le variabili promettenti.
2. Bucket più grandi → soluzioni di qualità più alta, ma tempi computazionali più alti.
3. Problemi complessi (m=30) richiedono l'**IKS** (buckets scorsi più di una volta).

## Applicazione 2: Portfolio Selection Problem con CVaR

### Descrizione del problema

Selezionare un portafoglio di securities che **massimizza il Conditional Value at Risk (CVaR)** (rendimento atteso nel percentile peggiore), soggetto a:
- Rendimento netto del portafoglio (costi fissi fⱼ e proporzionali cⱼ per ogni security j)
- Vincoli di capitale (lower e upper bound C₀, C₁)
- Vincolo di cardinalità (al più m securities selezionate)
- Vincoli di soglia (min e max valori lⱼ, uⱼ per ogni security j)

**Complessità**: Mixed Integer Binary Problem (due famiglie di variabili: xⱼ ≥ 0 intero + zⱼ ∈ {0,1}).

### Formulazione matematica

```
max  y − (1/β) Σₜ₌₁ᵀ pₜdₜ                               (11)
     dₜ ≥ Σⱼ(rⱼₜ − cⱼ)qⱼxⱼ − Σⱼ fⱼzⱼ    t=1,...,T    (12) perdite
     Σⱼ(rⱼₜ − cⱼ)qⱼxⱼ − Σⱼ fⱼzⱼ ≥ μ₀Σⱼqⱼxⱼ          (13) rendimento
     C₀ ≤ Σⱼ qⱼxⱼ ≤ C₁                                  (14) capitale
     Σⱼ zⱼ ≤ m                                           (15) cardinalità
     lⱼzⱼ ≤ xⱼ ≤ uⱼzⱼ    j=1,...,n                     (16) soglie
     dₜ ≥ 0, xⱼ ≥ 0 intero, zⱼ ∈ {0,1}                (17-19)
```

Dove rⱼₜ = rendimento storico della security j nello scenario t, pₜ = probabilità scenario t, β = livello di confidenza CVaR.

### KS settings per Portfolio

- **Sorting criterion** (su variabili x intere): reduced costs r
  - **SORTING A**: |rⱼ|/lⱼ non decrescente (normalizza per il lower bound)
  - **SORTING B**: |rⱼ| non decrescente (assoluto puro)
  - **SORTING C**: random
- **Kernel Set**: C = numero di variabili x selezionate nella LP relaxation
- **Buckets per Basic KS** (8 combinazioni): Fixed-bucket(0,0), (1,1), (2,1), (1,2), (0.2,10), Fixed-bucket(1), Fixed-bucket(0.2), Variable-bucket(5,10), Shifted-bucket
- **Buckets per IKS** (5 combinazioni): tutte le euristiche con più di 1 bucket

### Risultati computazionali

**Setup**: CPLEX 9.0, Pentium 3GHz, RAM 2GB.

**Istanze**: n = 100, 200, 300, 400, 500, 600 securities; m = 10, 20 (max cardinality).
- 152 Italian + 224 French + 224 German securities.
- Historical realizations: weekly rates of return 1999-2000.

**Basic KS** — % istanze entro γ% dall'ottimo (Sorting A vs B vs C):

| Variante | Sort A (0%) | Sort B (0%) | Sort C (0%) |
|----------|-------------|-------------|-------------|
| FB(0,0) | 25% | 25% | 25% |
| FB(1,1) | 71% | 71% | 54% |
| FB(2,1) | 75% | 71% | 29% |
| **FB(1)** | **79%** | **67%** | **46%** |
| **VB(5,10)** | **79%** | **55%** | **46%** |
| **Shifted-b** | **79%** | **71%** | **46%** |

**IKS** — migliora ulteriormente i risultati, soprattutto con Sorting B:

| Variante | Sort A (0%) | Sort B (0%) | Sort C (0%) |
|----------|-------------|-------------|-------------|
| FB(1) | 100% | 100% | 79% |
| FB_3(1) | **83%** | **79%** | **71%** |
| FB_IKS(10) | 85% | 83% | 71% |

### Lezioni da Portfolio

1. La LP relaxation fornisce buoni segnali per identificare il set ottimale di variabili.
2. Selezionare il giusto set di variabili è un task critico (variabili intere o binarie?).
3. I **reduced costs** sono un criterio di sorting adeguato (meglio se si considera il bound delle variabili).
4. Bucket più grandi → qualità migliore ma tempi computazionali più alti.
5. Un buon sorting criterion applicato al **Basic KS** dà risultati simili alla variante Iterativa.

## Drawbacks di Kernel Search

Aspetti critici identificati dopo le applicazioni:

1. **Cosa succede se i reduced costs non forniscono un buon segnale?** (sorting inadeguato)
2. **Cosa succede se il Kernel Set cresce troppo?** (i problemi ristretti diventano troppo grandi)
3. **Ci sono buoni modi per identificare la correlazione tra variabili?** (per costruire bucket più intelligenti)
4. **Dovremmo controllare dinamicamente il tempo di soluzione dei MILP ristretti?** (time threshold adattivo)

Questi drawbacks aprono la strada a varianti avanzate del KS.

## Articoli correlati

- [[kernel-search]]
- [[ottimizzazione-combinatoria]]
- [[approssimazione-algoritmi]]
- [[gurobi]]

## Fonti

- `raw/OA_Kernel Search - applicazioni.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2024/2025.
