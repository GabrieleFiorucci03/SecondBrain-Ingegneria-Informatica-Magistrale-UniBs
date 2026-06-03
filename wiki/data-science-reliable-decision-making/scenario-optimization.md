---
tags: [scenario-optimization, ottimizzazione, garanzie, probabilità, convex, data-driven]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/[Campi & Garatti] - Introduction to Scenario Optimization_COMPILED.pdf
  - raw/introduction_COMPILED.pdf
  - raw/SLIDES_beta-density_COMPILED.pdf
  - raw/2008_paper_COMPILED.pdf
---

# Scenario Optimization

Lo Scenario Approach è un framework per l'ottimizzazione in presenza di incertezza che produce soluzioni con **garanzie probabilistiche certificate**. Sviluppato da Marco C. Campi e Simone Garatti (e collaboratori), è formalizzato nel libro *Introduction to the Scenario Approach* (SIAM, 2018) e nei paper fondativi del 2008/2009.

## Punti chiave

- **Idea centrale**: sostituire il problema di ottimizzazione con incertezza con uno su N campioni (scenari) dell'incertezza; la soluzione eredita garanzie probabilistiche sulla perdita futura.
- **Ipotesi chiave**: la funzione di costo ℓ(ν, δ) è **convessa** in ν per ogni δ fissato.
- **Scenario Program SP_N**: min_ν [max_{i=1,...,N} ℓ(ν, δᵢ)] — convex program risolvibile con strumenti standard.
- **Teorema di generalizzazione**: se N ≥ (2/ε)(ln(1/β) + d - 1), allora con probabilità ≥ 1-β la soluzione ν* è ε-risk guaranteed.
- **Violation probability**: la probabilità che un nuovo scenario violi ℓ* è ≤ ε; la sua distribuzione (campionaria) è approssimata da una Beta(d, N-d+1).
- **Applicazioni**: controllo robusto, portafoglio finanziario, machine learning, compensazione del rumore, stima con layer.

## Il problema di ottimizzazione con incertezza

Dato un parametro di incertezza δ ~ P (distribuzione ignota su Δ) e una variabile di decisione ν ∈ ℝ^{d-1}, il problema ideale è:

```
min_ν  min_{Δ_ε ⊂ Δ, P(Δ_ε)=1-ε}  max_{δ ∈ Δ_ε}  ℓ(ν, δ)
```

(approccio **chance-constrained** al livello di rischio ε). Questo è NP-hard in generale.

### Tre paradigmi classici

| Paradigma | Formulazione | Pro | Contro |
|---|---|---|---|
| **Worst-case** | min_ν max_{δ∈Δ} ℓ(ν,δ) | Garanzie assolute | Troppo conservativo |
| **Average** | min_ν E_P[ℓ(ν,δ)] | Compromesso bilanciato | Nessuna garanzia sulle code |
| **Chance-constrained** | min_ν max_{δ∈Δ_ε} ℓ(ν,δ) | Rischio regolabile con ε | Computazionalmente intrattabile |

Il **performance bundle** è la famiglia di curve ℓ(ν,δ) al variare di δ: il worst-case è il bordo superiore, l'average è la curva baricentrica, il chance-constrained è una famiglia di curve a livelli di rischio 1%, 2%, …

## Il Scenario Program

### Definizione

Siano δ₁, δ₂, …, δ_N campioni i.i.d. da P (gli *scenari*). Il **Scenario Program SP_N** è:

```
SP_N:  ν* = argmin_ν  max_{i=1,...,N} ℓ(ν, δᵢ)
```

Poiché il massimo di funzioni convesse è convesso, **SP_N è un programma convesso** → risolvibile in tempo polinomiale (CVX, YALMIP, ecc.).

- ν* = soluzione ottima (il **design**)
- ℓ* = valore ottimo (worst-case cost sui campioni osservati)

La domanda chiave: dato che ℓ* è calcolato su N campioni, quale garanzia abbiamo che P{δ: ℓ(ν*, δ) > ℓ*} sia piccola?

## Il Teorema di Generalizzazione (Theorem 1.1)

**Teorema** (Campi & Garatti, forma base): Per ogni ε ∈ (0,1) (*risk parameter*) e β ∈ (0,1) (*confidence parameter*), se il numero di scenari N soddisfa

```
N ≥ (2/ε) · (ln(1/β) + d - 1)
```

allora **con probabilità ≥ 1 − β** (sul campionamento degli scenari):

```
P{δ ∈ Δ : ℓ(ν*, δ) > ℓ*} ≤ ε
```

### Interpretazione dei due livelli di probabilità

- **ε** (*violation risk*): probabilità (sotto P) che un nuovo δ futuro produca una perdita superiore a ℓ*. Parametro di robustezza regolabile.
- **β** (*confidence*): probabilità che il campione osservato sia "cattivo" e la garanzia non valga. In pratica si sceglie β = 10⁻⁶ o meno; essendo sotto logaritmo, N cresce pochissimo.

**Esempio**: ε = 0.01, β = 10⁻⁶, d = 10 → N ≥ 200 · (13.8 + 9) = 4560 scenari.

### Sample complexity

La formula N ≥ (2/ε)(ln(1/β) + d − 1) lega tre quantità:
- **d**: dimensione del problema di ottimizzazione (numero di variabili di decisione)
- **ε**: precisione delle garanzie (quanto piccolo vuoi il rischio)
- **β**: confidenza (quanto affidabile deve essere la garanzia)

Notare che **N scala linearmente in d e come 1/ε** — molto meglio del worst-case.

## Distribuzione della Violation Probability (Risultato esatto — 2008)

La **violation probability** V(ν*) = P{δ: ℓ(ν*, δ) > ℓ*} è una variabile aleatoria (dipende dal campione di scenari usato).

### Bound binomiale (Campi & Garatti, SIAM 2008)

Il paper fondativo del 2008 stabilisce il **bound esatto** (uguale, non solo ≤) per problemi **fully-supported**:

```
P^N{V(x*_N) > ε} = Σ_{i=0}^{d-1} C(N,i) · εⁱ · (1−ε)^{N−i}
```

Per tutti i problemi convessi vale il **bound superiore** (tight per fully-supported):

```
P^N{V(x*_N) > ε} ≤ Σ_{i=0}^{d-1} C(N,i) · εⁱ · (1−ε)^{N−i}
```

Questo è il complemento della CDF di una Binomiale(N, ε) valutata in d−1: P(Bin(N,ε) < d). Il bound esatto del 2008 è **molto più stringente** del bound precedente C(N,d)·(1−ε)^{N−d} (Calafiore & Campi, TAC 2006).

**Tabella comparativa** (d=10, β=10⁻⁵):

| ε | N (bound 2008) | N_old (bound 2006) |
|---|---|---|
| 0.05 | 285 | 579 |
| 0.01 | 1171 | 3035 |
| 0.005 | 2942 | 8675 |
| 0.001 | 11749 | 41008 |

### Connessione con la distribuzione Beta

La distribuzione di V(x*_N) per problemi fully-supported è approssimativamente **Beta(d, N−d+1)**:

```
V(ν*) ~ Beta(d, N-d+1)  (marginal distribution for fully-supported)
```

### Effetto di N e d

- **N cresce** (più scenari, d fissato): la Beta si concentra verso 0 → garanzie più stringenti.
  - N=5, d=2: distribuzione larga, centrata attorno a 0.2
  - N=10, d=2: pic si sposta verso 0.1
  - N=40, d=2: distribuzione molto concentrata vicino a 0.05 = d/N

- **d cresce** (più variabili, N fissato): la Beta si sposta a destra → garanzie più deboli.
  - d=2, N=40: picco circa 0.05
  - d=4: picco circa 0.1
  - d=8: picco circa 0.2
  - d=16: picco circa 0.4

**Regola del pollice**: E[V(ν*)] ≈ d/N. Per avere violazione attesa dell'1%, servono circa N = 100d scenari.

## Constraint removal e scenario approach rilassato

Una variante importante: invece di soddisfare tutti gli N scenari, si **rimuovono** k scenari (i più restrittivi = i *support constraints*). La soluzione è meno conservativa ma le garanzie si aggiustano:

```
P{δ: ℓ(ν*_k, δ) > ℓ*_k} ≤ ε_k
```

dove ε_k dipende da N, d, k secondo una formula esplicita (vedi Teorema 3.9 del libro).

## Concetto di Support Constraint

Un vincolo δᵢ è un **support constraint** per SP_N se la sua rimozione cambia la soluzione ν*. Il numero di support constraints è al più d (=dimensione del problema). Questa proprietà è fondamentale per le dimostrazioni.

**Fully supported problem**: SP_N con esattamente d support constraints. Il caso generico.

## Applicazioni principali

### Portafoglio (Value-at-Risk)
- δᵢ = [R₁ᵢ, …, R^{d-1}_i]^T rendimenti giornalieri di d-1 asset
- SP_N: min_ν [max_i (−Σ νₖ Rᵢᵏ)]
- ℓ* = perdita massima storica con il portafoglio ν*
- Garanzia: con prob ≥ 1−β, la prob di una perdita futura > ℓ* è ≤ ε

### Compensazione del rumore (ARMA)
- Sistema: y_{t+1} = a yₜ + b uₜ + c wₜ + d w_{t-1}, compensatore: uₜ = ν₁wₜ + ν₂w_{t-1}
- δᵢ = [aᵢ, bᵢ, cᵢ, dᵢ]^T parametri incerti del sistema
- SP_N minimizza la varianza di y in scenari peggiori

### Stima con layer
- N punti (uᵢ, yᵢ) ∈ ℝ²; trovare il layer più sottile y ∈ [ν₁ + ν₂u + ν₃u², ...] che contiene tutti i punti
- SP_N: min_{ν₁,ν₂,ν₃} [max_i |yᵢ − [ν₁ + ν₂uᵢ + ν₃uᵢ²]|]

### Machine learning / Classificazione
- 170 pazienti con arresto cardiaco (Spedali Civili di Brescia, 2007), feature: AMSA, RMS, CF
- Classificatore costruito con scenario approach: prob di misclassificazione ≤ ε

## Articoli correlati

- [[introduzione]]
- [[probability-foundations]]
- [[noise-compensation]]
- [[sys-id-and-limits-learning]]

## Fonti

- raw/[Campi & Garatti] - Introduction to Scenario Optimization_COMPILED.pdf — Libro SIAM (2018), Capitolo 1 e 3
- raw/introduction_COMPILED.pdf — Slides introduttive del corso (Campi & Carè, Budapest Jan. 2026)
- raw/SLIDES_beta-density_COMPILED.pdf — Grafici della distribuzione Beta della violation probability
- raw/2008_paper_COMPILED.pdf — Campi & Garatti, "The Exact Feasibility of Randomized Solutions of Uncertain Convex Programs", SIAM J. Optim. 19(3), 2008
- raw/TAC-paper_COMPILED.pdf — Calafiore & Campi, "The Scenario Approach to Robust Control Design", IEEE TAC 51(5), 2006 — paper originale
