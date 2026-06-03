---
tags: [scenario-optimization, controllo, ARMA, ARMAX, compensazione-rumore, risk-return]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/SLIDES_noise compensation_COMPILED.pdf
  - raw/[Campi & Garatti] - Introduction to Scenario Optimization_COMPILED.pdf
---

# Compensazione Feedforward del Rumore

Esempio applicativo paradigmatico dello [[scenario-optimization]]: progettare un compensatore feedforward per un sistema ARMAX con parametri incerti, minimizzando la varianza dell'uscita con garanzie probabilistiche certificate.

## Punti chiave

- **Sistema ARMAX**: y_{t+1} = ay_t + bu_t + cw_t + dw_{t-1}, con w_t ~ WN(0,1) rumore bianco misurato.
- **Compensatore**: u_t = ν₁w_t + ν₂w_{t-1} (feedforward lineare in w); variabili di decisione ν = [ν₁, ν₂]^T.
- **Incertezza**: parametri δ = [a, b, c, d]^T ignoti, appartenenti a un insieme Δ parametrizzato da (σ₁, σ₂) ∈ [−1, 1]².
- **Scenario Program**: campionare N scenari δᵢ, risolvere il min-max della varianza su questi scenari.
- **Risultato**: ε = 0.5%, β = 10⁻⁷ → N = 5427; soluzione ν₁* = −0.9022, ν₂* = −0.9028, ℓ* = 5.8.
- **Garanzia**: varianza dell'uscita ≤ 5.8 per tutti i possibili impianti tranne una frazione ε = 0.5%.
- **Risk-Return Tradeoff**: eliminando k scenari supporto si abbassa ℓ* ma aumenta il rischio εₖ.

## Formulazione del problema

### Sistema e compensatore

```
Sistema ARMAX:  y_{t+1} = a y_t + b u_t + c w_t + d w_{t-1}
Compensatore:   u_t = ν₁ w_t + ν₂ w_{t-1}
```

Sostituendo il compensatore nel sistema, la varianza stazionaria di y_t è:

```
var[y_t] = [(c + bν₁)² + (d + bν₂)² + 2a(c + bν₁)(d + bν₂)] / (1 − a²)
```

Se i parametri a, b, c, d fossero noti, la soluzione ottima sarebbe banale:
```
ν₁* = −c/b,  ν₂* = −d/b  →  var[y_t] = 0
```

Ma a, b, c, d ∈ Δ sono **ignoti** → si ricorre allo Scenario Approach.

### L'insieme di incertezza Δ

Δ è definito come l'insieme parametrico:
```
a = (3.5σ₁² − 0.2)/(3σ₁² + 0.3) · (0.32σ₁ + 0.6)
b = 1 + σ₁σ₂²/10
c = [−0.01 + (σ₁ + σ₂²)²]/[0.02 + (σ₁ + σ₂²)²] · [1 − (σ₁−1)(σ₂−1)/2]
d = 0.05/[0.025 + (σ₁ + σ₂ − 2)²]
(σ₁, σ₂) ∈ [−1, 1]²
```

Il **performance profile** var[y_t] = f(σ₁, σ₂) per il compensatore ottimale su Δ mostra una superficie complessa con picchi e valli — impossibile ottimizzare analiticamente.

## Applicazione dello Scenario Approach

### Calcolo di N

Con ε = 0.005 (risk = 0.5%), β = 10⁻⁷ (confidenza altissima), d = 2 (variabili ν₁, ν₂):

```
N ≥ (2/ε)(ln(1/β) + d − 1) = 400 · (16.1 + 1) = 400 · 17.1 ≈ 6840
```

Usando la formula più stringente del libro: **N = 5427**.

### Il Scenario Program

Campionare (σ₁ᵢ, σ₂ᵢ) ∈ [−1,1]² → calcolare (aᵢ, bᵢ, cᵢ, dᵢ), poi risolvere:

```
min_{ν₁, ν₂} [ max_{i=1,...,5427}  [(cᵢ + bᵢν₁)² + (dᵢ + bᵢν₂)² + 2aᵢ(cᵢ + bᵢν₁)(dᵢ + bᵢν₂)] / (1 − aᵢ²) ]
```

Questo è un **programma convesso** (il numeratore è una forma quadratica convessa in ν; il denominatore 1 − aᵢ² > 0 è costante per ogni scenario). Risolvibile con CVX/YALMIP.

### Risultato numerico

```
ν₁* = −0.9022
ν₂* = −0.9028
ℓ* = 5.8
```

**Interpretazione certificata**: con probabilità ≥ 1 − 10⁻⁷,
```
P{(σ₁, σ₂) ∈ [−1,1]² : var[y_t] > 5.8} ≤ 0.005
```

## Risk-Return Tradeoff: Scenario Removal

Una volta trovata la soluzione con tutti N = 5427 scenari, si può **eliminare progressivamente k scenari** (i più vincolanti = i support constraints) ottenendo:
- ℓ*ₖ = valore ottimo con k scenari rimossi (decresce)
- εₖ = rischio garantito aggiornato (cresce)

| k rimossi | ℓ*ₖ (performance) | εₖ (rischio) |
|---|---|---|
| 0 | 5.8 | 0.5% |
| 20 | ~4.0 | ~1% |
| 60 | ~2.0 | ~2% |
| 120 | ~1.5 | ~3% |
| 160 | ~1.0 | ~4% |

**Trade-off**: ridurre la varianza garantita costa in termini di rischio che la garanzia non valga per una frazione maggiore di impianti. L'utente sceglie il punto desiderato sulla curva ℓ* vs εₖ.

Il **profilo di performance** del compensatore ottimale mostra la varianza come funzione di (σ₁, σ₂): il piano orizzontale a ℓ* = 5.8 è il tetto garantito, violato solo per una piccola area rossa (ε = 0.5%) in Δ.

## Connessione con il contesto più generale

Questo esempio mostra il workflow completo dello Scenario Approach:
1. **Modellare l'incertezza** come distribuzione su Δ (qui uniforme su [−1,1]²)
2. **Campionare** N scenari i.i.d.
3. **Formulare** SP_N come programma convesso
4. **Risolvere** con un solver standard
5. **Leggere** la garanzia direttamente da ε e β (nessuna analisi aggiuntiva)
6. **Navigare** il risk-return tradeoff rimuovendo scenari se necessario

## Articoli correlati

- [[scenario-optimization]]
- [[introduzione]]
- [[sys-id-and-limits-learning]]

## Fonti

- raw/SLIDES_noise compensation_COMPILED.pdf — Slides del corso (Campi)
- raw/[Campi & Garatti] - Introduction to Scenario Optimization_COMPILED.pdf — Libro SIAM (2018), sezione 1.2.1 e Capitolo 2
